There are a few different ways that 'file uploads' are performed in HTTP and REST applications:

* Multi-part Forms
* Raw Data Uploads

Actual file uploads that simulate HTML File upload buttons are done using **multi-part forms** which are specialized version of form uploads. **Raw uploads** are commonly used in REST APIs that have upload URLs that simply accept raw file data as input. 

### Multi-part Form Uploads
You can simulate an HTTP form upload with wwHTTP by using multi-part form uploading mode with `.nHttpPostMode=2`. All that is needed is a server application that normally processes file uploads as would be expected of an HTTP form file upload button. 

An example can be found on the <a href="https://west-wind.com/wconnect/wcscripts/fileupload.wwd" target="top">Web Connection Demo Page</a> using **Standard HTML Form Upload** sample.


```foxpro
DO wwHttp
oHttp = CREATEOBJECT("wwHTTP")

*** Set mode to multi-part form
oHttp.nHttpPostMode = 2

*** Post a file as a Form variable
oHttp.AddPostFile("upload","c:\sailbig.jpg",.T.,"image.png","image/png")

*** Optionally there may be other basic form variables on the form
oHttp.AddPostKey("notes","Notes about the image uploaded")

lcHTML = oHTTP.Post("http://localhost/wconnect/wcscripts/FileUpload.wwd")

*** Display result from server
ShowHTML(lcHTML)  && Image is there but not rendering because of relative path
```

The important part is to set `oHttp.nPostMode=2` (multi-part forms) which is required in order to upload files via HTTP. Multipart mode is much more efficient than standard URL Encoded format as raw binary data is sent to the server un-encoded which results in smaller files and raw data that doesn't have to be converted first. Most upload operations on the Web use multi-part forms for upload purposes.

You need to use `.AddPostKey(lcKey,lcFilePath,.T.)` to actually send the file. The last `.T.` parameter specifies that `lcFilePath`  is a filename and loaded from disk rather than a raw string. wwHttp reads the file directly from disk and encodes it. Alternately you can also pass the raw data as a string and leave the parameter at its default of `.F.`. 

Files uploaded from disk can exceed 16 megs. Raw string form values cannot exceed 16 megs. 

> Note Web Connection server requests can only receive a POST buffers of up to 16 megs total

### Picking up an uploaded file on the Server with Web Connection
The file must be picked up by a server application running on the Web Server. In the example above a `FileUpload.wwd` handler in a Web Connection application is picking up the file which looks something like this.

The relevant HTML elements in Html are:

```html
<form enctype="multipart/form-data" method="POST" action="/wconnect/FileUpload.wwd">
   **<input type="file" name="Upload">**  
   File Description:
   <textarea name="txtFileNotes" rows="4"></textarea>
   <input type="submit" name="btnSubmit" value="Upload File"> 
</form>
```

And the Web Connection Process class code to pick up the file and notes:

```foxpro
FUNCTION FileUpload
************************************************************************
* wwDemo :: FileUpload
*********************************
***  Function: Demonstrates how to upload files from HTML forms
************************************************************************
lcFileName = ""
lcFileBuffer = Request.GetMultiPartFile("Upload",@lcFileName)
lcNotes = Request.Form("txtFileNotes")
lcFileName = SYS(2023)+"\"+lcFileName

IF LEN(lcFileBuffer) = 0
   THIS.StandardPage("Upload Error","An error occurred during the upload or the file was too big.")
   RETURN
ENDIF
IF LEN(lcFileBuffer) > 500000
   THIS.StandardPage("File upload refused",;
                     "Files over 500k are not allowed for this sample...<BR>"+;
                     "File: " + lcFilename)
   RETURN
ENDIF
   
*** Now dump the file to disk
File2Var(lcFileName,lcFileBuffer)

THIS.StandardPage("Thank you for your file upload",;
                  "<b>File Uploaded:</b> " + lcFileName + ;
                  " (" + TRANSFORM(FileSize(lcFileName),"9,999,999") + " bytes)<p>"+ ;
                  "<b>Notes:</b><br>"+ CRLF + lcNotes )

ENDFUNC
* wwDemo :: FileUpload
```

The key here is the Request.GetMultiPartFile() method which retrieves the file by its key name. Pass in an option filename string by reference to also retrieve the file name the browser sent.

Note that uploads in Web Connection are limited to 16 megs total content due to FoxPro's 16meg string limit.


### Using an ASP.NET Web Form to receive the File
Here's another server side example of what C# code in an ASP.Net page might look like to pick up a POSTed file:

```csharp
private void Page_Load(object sender, System.EventArgs e)
{
	// *** Assume Posted variable is "File" 
	HttpPostedFile File = Request.Files["Upload"];
	
	// Put user code to initialize the page here
	if (File == null) 
	{
		this.lblErrorMessage.Text = "No File uploaded";
		return;
	}

	if (File.ContentLength > 200000) 
	{
		this.lblErrorMessage.Text = "File too large.";
		return;
	}

	string Filename = Request.PhysicalApplicationPath + "itemimages\\" + 
		              (new System.IO.FileInfo(File.FileName)).Name;

	File.SaveAs(Filename);

	this.lblErrorMessage.Text = "Upload saved";
}
```

### Raw REST Uploads
Some services also allow uploading of 'files' as raw data. 

So rather than using the multi-part forms protocol, you simply send the raw file data to the server in a `POST` or `PUT` operation. Frequently services will require an additional header to specify a file name or additional information about the file. To do this, set the `.cContentType` property explicitly, or explicitly set the `.nHttpDataMode=4`.


```foxpro
DO wwHttp
oHttp = CREATEOBJECT("wwHTTP")

*** Set mode to multi-part form
* oHttp.nHttpPostMode = 4
oHttp.cContentType = "application/json"

*** Add any headers the service might request
*** this is app specific
oHttp.AddHeader("x-filename","customer.json")

lcJson = GetJsonCustomer()

*** Just post the raw data
oHttp.AddPostKey(lcJson)

*** Call server and receive response string
lcJson = oHTTP.Post("https://localhost/MyApp/Api/CustomerUpload")

*** Display result from server
ShowText(lcJson)  
```

As an alternative you can also directly post data using the `Post()` method:

```foxpro
*** Need to set the content type
oHttp.cContentType = "application/json"

*** Call server and receive response string
lcJsonResult = oHTTP.Post("https://localhost/MyApp/Api/CustomerUpload", lcJson)
```

If you build your own services I would recommend using this latter approach as it is more efficient and easier to implement for REST clients. However, it does mean that you can only send one thing per HTTP request and that you need to use HTTP headers to send any additional information related to a file which is limited (you can't send multiline content in a header for example).

Pick the right mechanism for the job.

### Posting files larger than 16megs
FoxPro has a 16 megabyte string limit. However, wwHttp support POST buffers larger than 16 megs. You can use AddPostKey() with a file name that gets loaded directly from disk with files of any size. Individual POST strings must be smaller than 16 megs, but the combined POST buffer may exceed 16 megs.

Note that AddPostKey() and wwHttp allow for uploads larger than 16 megs but any upload larger than 16 megs must be a file based upload using AddPostKey("FileKey",lcFilePath,.T.) as this is the only reliable way to get strings into the post variables in one shot without modification. 

If you use plain string AddPostKey values ( .AddPostKey("lcKey",lcLargeString)) that are larger than 16 megs you will get an error.