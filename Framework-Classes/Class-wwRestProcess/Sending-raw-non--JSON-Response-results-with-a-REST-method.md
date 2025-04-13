REST services by default create JSON output, but it's also possible to create raw Response output that allows you to return **any kind of HTTP output** including binary files (PDF, Images, audio, video) or even other text output like  HTML, Xml or text based resources like JavaScript, CSS etc.

To do this you can use one of two properties in your REST methods:

* **THIS.lRawResponse**  *(new as of 7.41)*
* **THIS.oJsonService.IsRawResponse**

### Returning non-Json Results
There are a couple of ways to return non-JSON results:

* From Memory Variables
* From Files

#### From Memory
Here's how you can return a non-JSON response in a REST method from memory:

```foxpro
FUNCTION ReturnPdf()

THIS.oJsonService.IsRawResponse = .T.
Response.ContentType = "application/pdf"

 lcFilename = THIS.ResolvePath("~/policy.pdf") 

*** Send from memory - string/blob
lcFile = FILETOSTR(lcFilename)
Response.BinaryWrite( lcFile )

ENDFUNC
```

Other output options might include using other HTML based response methods like `Response.ExpandScript()` to render a full HTML page. This basically gives you the ability to truly match REST and HTML content.

The following demonstrates returning an HTML response from a REST request:

```foxpro
FUNCTION Index()

*** We take over the Response here - no JSON Serialization
JsonService.IsRawResponse = .T.

Response.ContentType = "text/html"
Response.ExpandTemplate("~/index.wcs")

ENDFUNC
```
> #### @icon-warning Specify the Content Type
> You should **always** explicitly set the `Content-Type` of the request via `Response.ContentType`. In REST requests the default is `application/json`, so in most raw requests you'll need to change the content type to match your result text or data.

#### From File
If you need to return content from file, you can use:

* Response.DownloadFile()
* Response.TransmitFile()
 
These two methods vary in how files are presented in the browser - the former downloads always, while the latter will display in the browser if an appropriate viewer is available. For example a PDF file will download with the `DownloadFile()` but display in the browser PDF viewer with `TransmitFile()`.
 
```foxpro
FUNCTION ReturnPdf()

THIS.oJsonService.IsRawResponse = .T.
Response.ContentType = "application/pdf"

lcFilename = THIS.ResolvePath("~/policy.pdf") 

*** Send from file
Response.TransmitFile(lcFilename,"application/pdf")

ENDFUNC
```