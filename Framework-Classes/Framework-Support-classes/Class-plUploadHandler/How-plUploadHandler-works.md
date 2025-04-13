plUpload is a popular client JavaScript upload component and plUploadHandler provides the Web Connection backend functionality to accept files 

### Getting plUpload
plUpload can be gotten from the plUpload Web site at plUpload.com. You can download the Zip file to get the latest version. Web Connection ships with the plUpload client libraries in the scritps/plUpload folder for each Web site created. If you want to install the latest version you can simply replace the files in /scripts/plupload with the latest files retrieved from the plUpload site.

### Client Side: Adding the plUpload Scripts and CSS
plUpload is a JavaScript and HTML component so you need to add some files to your project. Starting with Web Connect 5.66 the plUpload files ship with Web Connection. Prior installs can download plUpload and copy the following files from the distribution:

![](IMAGES%2Fmisc%2FplUploadScripts.png)

Everything related to plUpload - scripts, css and images - lives under the /scripts/plupload folder so it's fully self contained.

Once you've added the required scripts to the Web site they need to be loaded into the page.

First add the CSS to your header:

```html
<link href="../scripts/plupload/jquery.plupload.queue/css/jquery.plupload.queue.css" 
       rel="stylesheet"
       type="text/css" />
```

Then add the required scripts:

```html
<!-- plUploadQueue requires jQuery -->
<script src="../scripts/jquery.min.js" type="text/javascript"></script>

<!-- Add plUpload base and the queue UI component
<script src="../scripts/plupload/plupload.full.min.js"></script>    
<script src="../scripts/plupload/jquery.plupload.queue/jquery.plupload.queue.js"></script>
```

### Setting up the Client Script and HTML
plUploadQueue is a UI component that wraps the basic plUpload component which is a non-visual component. It provides a nice looking UI to select and display files and upload progress:

![](IMAGES%2Fmisc%2FplUploadQueueComponent.png)

The component loads into the client document based on an empty <div> tag placed into the document, which is then filled by the component.

```html
<div id="Uploader">        
</div>
```

Script code then creates the plUpload component and effectively renders the uploader HTML into it:

```javascript
<script>
$(document).ready(function () {
    // initialize status bar
    showStatus({ autoClose: true });

    $("#Uploader").pluploadQueue({
        runtimes: 'html5,flash,html4',
        url: 'plUpload.wwd',
        
        chunk_size: '64kb',
        unique_names: false,
        // Resize images on clientside if we can
        resize: { width: 1024, height: 768, quality: 90 },
        // Specify what files to browse for
        filters: [
            {
                max_file_size: '2mb',
                title: "Image files",
                extensions: "jpg,gif,png"                
            }],
        flash_swf_url: '../scripts/plupload/Moxie.swf',
        silverlight_xap_url: '../scripts/plupload/Moxie.xap',
        multiple_queues: true
    });

    // get uploader instance
    var uploader = $("#Uploader").pluploadQueue();


    // bind uploaded event and display the image
    // response.response returns the last response from server
    // which is the URL to the image that was sent by OnUploadCompleted
    uploader.bind("FileUploaded", function (upload, file, response) {
        // remove the file from the list
        upload.removeFile(file);

        // Response.response returns server output from onUploadCompleted
        // our code returns the url to the image so we can display it
        var imageUrl = response.response;

        $("<img>").attr({ src: imageUrl })
                    .click(function () {
                        $("#ImageView").attr("src", imageUrl);
                        setTimeout(function () {
                            var ip = $("#ImagePreview");

                            // show as overlay
                            ip.fadeIn("slow")
                                .modalDialog()
                                .closable({ cssClass: "closebox-container", closeHandler: function () { $("#ImagePreview").modalDialog("hide"); } })
                                .draggable();

                            // close the modal by clicking on the overlay
                            $("#_ModalOverlay").click(function () {
                                $("#ImagePreview").modalDialog("hide");
                            });
                        }, 200);
                    })
                    .appendTo($("#ImageContainer"));
    });

    // Error handler displays client side errors and transfer errors
    // when you click on the error icons
    uploader.bind("Error", function (upload, error) {            
        showStatus(error.message, 3000, true);
    });

    // only allow 5 files to be uploaded at once
    uploader.bind("FilesAdded", function (up, filesToBeAdded) {
        if (up.files.length > 5) {
            up.files.splice(4, up.files.length - 5);
            showStatus("Only 5 files max are allowed per upload. Extra files removed.", 3000, true);
            return false;
        }
        return true;
    });

    //The plUpload labels are not customizable explicitly
    //so if you want to do this you have to directly manipulate the DOM
    setTimeout(function () {
        $(".plupload_header_title").text("Upload Images")
        $(".plupload_header_text").html("Add images to upload and click start. Images are resized to 600 pixels height and can't be larger than 1 meg.")
    }, 200);

});
</script>
```  

The key piece is the .pluploadQueue() function which configures the uploader. 

First you have to specify which runtimes to support. Runtimes here means the technology that performs uploading. Sadly no one technology works everywhere so multiples are required and plUpload supports many. I use HTML 5 as the first and best option, falling back to Silverlight if available and then Flash, and finally Html 4 plain uploads. Most modern browsers support HTML5 so that will be the most common usage scenario these days.

The URL is the server side relative URL that will receive uploaded file chunks - I'll talk about the implementation of this handler in the next section.

The rest of the settings should be straight forward. Important are that you specify the relative URLs to the Flash and Silverlight components.

Once the UI component has been created you can also get a reference to the underlying lower level plUpload component, which can then be used to affect the behavior of the uploader itself and attach events. A common event you'll want to trap is the *FileUpload* event, so you can take action when a file uploaded. For example, if you're uploading images you might want to display the uploaded image in your HTML form. FileUploaded receives a *response.response* result which contains the final output from the server which commonly will be a URL that points at the uploaded file. But what is returned could be anything including a complex JSON object with more information about the uploaded file or related activities.

You'll also want to implement the client side *Error* event to ensure you display some message that tells what went wrong.

### Server Side plUploadHandler Implementation in Web Connection
The server side is implemented using a small Web Connection component called plUploadHandler. This handler is a self contained processor that can handle the chunked file messages that plUpload sends when uploading files. The component can either be subclassed to customize several 'event hook' methods like OnUploadCompleted(), OnUploadStarted() or by hooking these same 'events' by using FoxPro's BINDEVENT().

The following example is an image uploader that returns back the URL of the image when a file completes uploading that is implemented on a Web Connection wwProcess derived class (this example lives in wwDemo.prg):

```foxpro
FUNCTION plUpload()
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
*  plUpload
* *** *** *** *** *** *** *** *** *** *** *** *** ***
* **  Function: Example handler implementation that can accept 
* **            plUpload file requests.
* **    Assume: Calls OnUploadComplete function
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **

* ** Make sure plUploadHandler is loaded here or in your wwServer::OnLoad()
* SET PROCEDURE TO plUploadHandler ADDITIVE 

LOCAL loUpload as plUploadHandler
loUpload = CREATEOBJECT("plUploadHandler")

* ** Upload to temp folder
loUpload.cUploadPath = ADDBS(THIS.oConfig.cHtmlPagePath) + "temp"

BINDEVENT(loUpload,"OnUploadComplete",THIS,"OnUploadComplete",1)

* ** Constrain the extensions allowed on the server
loUpload.cAllowedExtensions = "jpg,jpeg,png,gif"

* ** Process the file or chunk
loUpload.ProcessRequest()

ENDFUNC


FUNCTION OnUploadComplete(lcFilename, loUpload)
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
*  OnUploadComplete
* *** *** *** *** *** *** *** *** *** *** *** *** ***
* **  Function: The plUpload OnCompletion function that is called
* **            when the upload is complete. Resizes images
* **            to a specific size.
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
LOCAL lcUrl, lcFile

lcUrl = this.ResolveUrl("~/temp/" + lcFileName)

* ** Resize the image
lcFile = ADDBS(loUpload.cUploadPath) + lcFileName
ResizeImage(lcFile,lcFile,640,480)

* ** Delete expired files - only for 10 minutes
DeleteFiles(ADDBS(JUSTPATH(lcFile)) + "*.*",600)

* ** Write out the response for the client (if any)
* ** In this case the URL to the uploaded image
loUpload.WriteCompletionResponse(lcUrl)

ENDFUNC
*   OnUploadComplete
```

The key is the plUploadHandler object instance to which you simply assign a path where the file chunks are uploaded to. You can specify a list of extensions that are allowed and a max upload size of the file uploaded to limit uploads to some extent. The JavaScript client also checks for these things, but it's important to also check these on the server.

To provide custom functionality when a file upload has completed you can either subclass the plUploadHandler class and override the OnUploadComplete() (or any of the other Onxxx methods), or as I've done in this example, simply use BINDEVENT() to route the call to another  method in this Process class.

```foxpro
BINDEVENT(loUpload,"OnUploadComplete",THIS,"OnUploadComplete",1)
```

This bind the OnUploadComplete() method of the plUpload component to the OnUploadComplete method in the process class, which is often simpler than subclassing. When a file is complete the OnUploadComplete() method is fired and your custom code runs. In the example of image uploads the image is resized and old files are timed out and deleted to keep the uploads folder clean. Other things you might do here is copy the uploaded files to a different folder or store them in a database etc.

Finally you should always call loUpload.WriteCompletionResponse() to write out a string response that the client picks up as response.response in the *FileUploaded* event on the client as shown earlier.

### Alternate Storage
If you want to store data directly into a database or do something other than writing the data to a file, you can subclass plUpload and override the OnUploadChunk() method. This method is called as each chunk of data is received and you can then write each chunk to whatever store you choose. This is more low level behavior so take a look at the base implementation of OnUploadChunk to see how to handle errors and return error messages to the client.