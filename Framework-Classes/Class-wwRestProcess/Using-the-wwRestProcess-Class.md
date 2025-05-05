Implementing the wwRestProcess class is easy:

* Create a new Process class with the Console Wizard
* Choose **Rest API Service Process Class** on the Finish page
* Or: Manually change the `WWC_PROCESS` process base class to `WWC_RESTPROCESS`
* Implement methods that act as REST HTTP endpoints
* Accept a single parameter which is deserialize from POST and PUT JSON content
* Return values or objects to be serialized to JSON or XML
* Call the methods using standard Method.ext call syntax
* Optionally set up [Extensionless URL Routing](VFPS://Topic/_3K812UDQ3)


### Create the REST Service
You create a `wwRESTProcess` class like any other process class - by using the Web Connection Console and the **Create New Process Class** Wizard . On **Step 2** choose **REST API Service Process class**. In this example, I'm also going to create a *MusicStoreProcess* (see Web Connection Sample) with a script map of *.ms*.

![](/images/ManagementConsole/CreateRestProcessClass.png)

This produces:

```foxpro
*************************************************************
DEFINE CLASS MusicStoreProcess AS WWC_RESTPROCESS
*************************************************************
```

You now have a REST capable Process class.

### Implementing REST EndPoints
This REST process subclass provides for automatic routing of requests to non-HTML endpoints that send and return JSON (and eventually XML). 

Just like standard Web Connection requests a URL's script map (or classic ~ syntax) routes to a Process method in the REST service. So GetAlbums.ms will route to a GetAlbums method in the process class.

The implemented method typically has to know nothing about Web Connection, but is simply a method that receives an input parameter and returns a result value that is serialized. Typical REST endpoints do very little work - they tend to call a business object to do some work and return a result that is then serialized.

### Passing Parameters
You can pass parameters to your service methods in several ways:

* **A single JSON Object/Value**  
You can pass a single JSON formatted object/value in JS via POST/PUT buffer and an application/json content type and the REST process will parse the JSON object into a single FoxPro parameter:
```foxpro
*** Url: SaveAlbum.ms
*** POST data: { id: 123, descript: "...", ...}
*** Content-type: application-json
FUNCTION SaveAlbum(loAlbum)
*** ... code tp save/manipulate album
RETURN loUpdatedAlbum
```

* **QueryString or Form Parameters**  
As in regular Web COnnection Process classes you can also access the Request object and the Form() and QueryString() methods to retrieve explicitly passed HTTP parameters.  
```foxpro
*** Url: GetAlbum.ms?id=22
FUNCTION GetAlbum()
lnId = VAL(Request.QueryString("id"))
*** ... load album
RETURN loAlbum
```
```foxpro
*** Url: GetAlbum.ms
*** POST: id=23
*** Content-type: x-www-form-urlended
FUNCTION GetAlbum()
lnId = VAL(Request.Form("id"))
*** ... load album
RETURN loAlbum
```

* **CallbackMethod,Parm1,Parm2,Parm3 POST or QueryString Values**  
This follows the wwAjaxCallback Control model that is used for the Web Control Framework control. Here parameters are passed in either via QueryString or POST values, and parameters must follow known names of CallbackMethod or Method, and Parm1, Parm2, Parm3 using standard URL encoded submission values.  
  
  This mechanism is mainly provided to provide an easy way to migrate method from the wwAjaxCallbackControl to its own self-contained class and it also works with the ajaxCallMethod() function in ww.jquery.js.
```foxpro
* URL: GetFilteredAlbums.ms?parm1=10/12/2014&parm2=10/14/2014&CallbackParmCount=2
FUNCTION GetFilteredAlbums(lcStartDate,lcEndDate)
ldStart = CTOD(lcStartDate)
ldEnd = CTOD(lcEndDate)
*** ... loads based on filter conditions album
RETURN loAlbums
```


### Returning Result Values
The service can return most FoxPro values as JSON or XML. 

It can return the following types of values:

* **Simple Values**  
Any simple FoxPro values of type string, number, boolean, datetime as well as binary values.  
*RETURN 10*

* **Object**  
Objects can be nested and contain other objects, collections and arrays as well as cursors.  
*RETURN loAlbum*

* **Collection**  
A FoxPro or Web Connection Collection. Only works with FoxPro objects/values - not with COM objects.  
*RETURN loAlbumList*

* **Cursor/Table Results**  
Cursors can be returned by using a string value of `cursor:AliasName` which serializes the specified cursor or table alias. The cursor is serialized as a flat array with each cursor record turned into an object.  
*RETURN "cursor:AliasName"*


> ### @icon-info-circle No Support for Array Results
> There's no support for Array results because arrays cannot be 'returned' as a result value in FoxPro.
>
> You can use arrays in Array properties on objects however so you could wrap the array into an object property. If you absolutely have to return an array you can use `ArrayToCollection()` to turn the array into a collection and return that instead.

### Returning Raw, non-JSON Results
You can also return results that are not parsed by the JSON Service. IOW, you can return raw strings or an HTML rendered result like Response.ExpandTempalte(), Response.ExpandScript() or Response.ExpandPage() or generated output from one of the wwPdf classes.

To do this you use the private JSONService reference set the IsRawResponse property to .T.. Simply create a method in your REST service process class and use standard Response methods like Write(), ExpandTemplate(), ExpandScript() etc. or any other helper class like wwPDF that generates into the Response to create your output:

```foxpro
FUNCTION Index()
JsonService.IsRawResponse = .T.

*** You have to set the Content Type because
*** the default is application/json in the REST service
Response.ContentType = "text/html"

*** Write output directly into the Response object
*** Response is just written
Response.ExpandTemplate(Request.GetPhysicalPath())
ENDFUNC
```
### Customizing the JSON Parsing
As with the raw response you can use the `JsonService` instance to customize behavior of the service, and `Serializer` to replace or customize the serializer with custom settings. 

These objects are `PRIVATE` variables in scope inside of REST process methods and can be accessed at will.

```foxpro
*** REST Service Endpoint Method (customerlist.wwd)
FUNCTION CustomerList()

SELECT * from Customers into cursor TQuery

*** override property names
Serializer.cPropertyNameOverrides = "firstName,lastName,lastAccess"

*** Can be set to return non-JSON-serialized results
JsonService.IsRawResponse = .F. 

RETURN "cursor:TQuery"
```

### Error Handling
The wwRestServices wraps the REST method handlers into an Exception block and then generates a JSON error response if an error occurs.

```json
{
    "isCallbackError": true,
    "message": "Error message returned here"
}
```

This is useful both for trapping unexpected errors and passing them to the front end or other caller, but it can also be a good option for transmitting error messages from your code to the client. For example if a validation fails or an invalid operation was performed you can simply thrown an error.

```foxpro
IF !loUser.HasGroup("Administrators")
    ERROR "This operation requires Administrators access"
ENDIF

* ... continue processing
```
which then allows your code to check for an error response that can display this error message.

### Working with HTTP Verbs
'Proper' REST services take advantage of HTTP verbs to expose HTTP endpoints. A common scenario for REST services is to use a single URL for display, update and delete operations, using HTTP Verbs instead to differentiate requests.

Since Web Connection routes requests to methods and methods cannot be overloaded in FoxPro you need to manually manage HTTP verbs in requests if you want to use a single URL. You can use a single method to handle GET, POST, PUT and DELETE operations and then check for use Request.GetHttpVerb() to check which verb is used and then route to other methods.

For example, the following implementation of Album.ms handles GET, POST/PUT and DELETE operations using a single *Album.ms* URL:

```foxpro
************************************************************************
*  Album
****************************************
FUNCTION Album(loAlbum)

lcVerb = Request.GetHttpVerb()

*** Handle alternate verbs with same URL with
*** separate method since we don't have method overloading in VFP
IF (lcVerb == "POST" OR lcVerb == "PUT")
   RETURN THIS.SaveAlbum(loAlbum)
ENDIF

IF lcVerb == "DELETE"
   RETURN this.DeleteAlbum()
ENDIF   

*** GET looks for querystring
lnId = VAL(Request.QueryString("id"))
IF (lnId < 1)
   ERROR "Invalid Id passed"
ENDIF

loBusAlbum = CREATEOBJECT("cAlbum")
IF (!loBusAlbum.Load(lnId))
   ERROR loBusAlbum.cErrorMsg
ENDIF

RETURN loBusAlbum.oData
*   Album

************************************************************************
*  SaveAlbum
****************************************
FUNCTION SaveAlbum(loAlbum)
LOCAL lnId, album, loBusAlbum

IF VARTYPE(loAlbum) # "O"
   ERROR "No album provided to save."
ENDIF   

lnId = loAlbum.pk

loBusAlbum = CREATEOBJECT("cAlbum")

IF (lnId < 1 OR !loBusAlbum.Load(lnId))
   *** Create new instance
   loBusAlbum.New()
   lnId = loBusAlbum.oData.Pk
ENDIF

loBusAlbum.oData = loAlbum
loBusAlbum.oData.Pk = lnId

IF !loBusAlbum.Save()
   ERROR "Unable to save album"
ENDIF

*** Return the album with update data
RETURN loBusAlbum.oData
*   SaveAlbum

************************************************************************
*  DeleteAlbum
****************************************
FUNCTION DeleteAlbum()

lnId = VAL(Request.QueryString("id"))

loBusAlbum = CREATEOBJECT("cAlbum")
IF !loBusAlbum.Delete(lnId)
   ERROR loBusAlbum.cErrorMsg
ENDIF

RETURN .T.
*   DeleteAlbum
```

### Testing REST Endpoints
When building REST APIs it's quite useful to have a good way to test APIs without having to run an application against it. There are a number of tools available that can help with this process.

* **<a href="http://telerik.com/fiddler" target="top">Fiddler's Composer</a>**  
One of the tab in the Fiddler HTTP Proxy is the Composer tab that lets you enter URLs and request data to play back repeatedly. Works well for single URLs.

* **<a href="https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm?hl=en" target="top">PostMan Chrome Plug-in</a>**  
Postman is a flexible Chrome plug-in and Chrome App that allows you to create requests and also save them to lists that you can refer back to.

* **<a href="https://websurge.west-wind.com" target="top">West Wind Web Surge</a>**  
Although West Wind WebSurge is billed as a load testing tool it also works as an excellent HTTP testing tool that lets you capture URLs and play them back either one by one for testing or under load for stress testing a Web site.

<a href="http://west-wind.com/websurge" target="top">![](/images/WebSurgeApiTesting.png)</a>


### Example Service Implementation
The MusicStore sample that ships with Web Connection in the wwDemo/MusicStore folder provides a full REST service implementation that you can take a look at. Here's a small excerpt of that service implementation.

```foxpro
*************************************************************
DEFINE CLASS MusicStoreProcess AS WWC_RESTPROCESS
*************************************************************

*********************************************************************
* Function MusicStoreProcess :: OnProcessInit
************************************
*** If you need to hook up generic functionality that occurs on
*** every hit against this process class , implement this method.
*********************************************************************
FUNCTION OnProcessInit

*** Force GZip on result if browser requests
Response.GzipCompression = .T.

RETURN .T.
ENDFUNC

************************************************************************
*  Artists
****************************************
FUNCTION Artists()
LOCAL loBusArtist, loArtists, loEnv

#IF .F. 
LOCAL Request as wwRequest, Response as wwPageResponse
#ENDIF

loBusArtist = CREATEOBJECT("cArtist")

loArtists = null

*loEnv = CREATEOBJECT("wwEnv","EngineBehavior","70")
lnEB = SET("ENGINEBEHAVIOR")
SET ENGINEBEHAVIOR 70
lnCount = loBusArtist.Query("Select distinct Artists.*, "+;
                  "(select COUNT(pk) from albums " + ;
                  "   where artistPk = artists.pk) " +;
                  "   as Count from Artists, Albums " +;
                  "WHERE artists.pk == albums.artistPk " +;
                  "ORDER BY artists.artistName",;
                  "TArtists",64)                  
SET ENGINEBEHAVIOR (lnEb)
                     
IF (lnCount > 0)
   loArtists = loBusArtist.vResult
ELSE
   ERROR "No artists could be found."
ENDIF

RETURN loArtists
ENDFUNC
*   Artists

************************************************************************
*  Artist
****************************************
FUNCTION Artist(loArtist)

#IF .F. 
LOCAL Request as wwRequest, Response as wwPageResponse
#ENDIF

lcVerb = Request.GetHttpverb()

*** Handle REST Operations
IF lcVerb = "POST" OR lcVerb = "PUT"
   *** Update or create new artist
   RETURN THIS.UpdateArtist(loArtist)  && updated/new artist
ENDIF
IF lcVerb = "DELETE"
   loBusArtist = CREATEOBJECT("cArtist")   
   RETURN loBusArtist.Delete(lnPk)  && .T. or .F.
ENDIF

lnPk = VAL(Request.QueryString("id"))

loBusArtist = CREATEOBJECT("cArtist")

IF (!loBusArtist.Load(lnPk))
   ERROR loBusArtist.cErrorMsg
ENDIF   

loBusArtist.LoadAlbums()

RETURN loBusArtist.oData
ENDFUNC
*   Artist

************************************************************************
*  UpdateArtist
****************************************
FUNCTION UpdateArtist(loArtist)

IF VARTYPE(loArtist) # "O"
   ERROR "Invalid data passed."
ENDIF

lnPk = loArtist.pk

loBusArtist = CREATEOBJECT("cArtist")
IF lnPk = 0
   loBusArtist.New()
ELSE
   IF !loBusArtist.Load(lnPk)
      ERROR "Invalid Artist Id."
   ENDIF
ENDIF 

loArt = loBusArtist.oData
loArt.Descript = loArtist.Descript
loArt.ArtistName = loArtist.ArtistName
loArt.ImageUrl = loArtist.ImageUrl
loArt.AmazonUrl = loArtist.AmazonUrl

IF !loBusArtist.Validate() OR ! loBusArtist.Save()
    ERROR loBusArtist.cErrorMsg
ENDIF

loBusArtist.LoadAlbums()

RETURN loArt
ENDFUNC
*   UpdateArtist

************************************************************************
*  Albums
****************************************
FUNCTION Albums()
LOCAL loBusAlbum, loAlbums

loBusAlbum = CREATEOBJECT("cAlbum")
loAlbums = null

*** Load albums individually and then load
*** related artist and songs via bus object
*** this way we get a nested JSON structure
IF loBusAlbum.GetAlbumPkList() > -1
   loAlbums = CREATEOBJECT("Collection")
   SCAN
         loBusAlbum.Load(TAlbums.Pk)    
         loAlbums.Add(loBusAlbum.oData)
   ENDSCAN
   USE IN TAlbums
ENDIF

RETURN loAlbums
ENDFUNC
*   Albums

************************************************************************
*  Album
****************************************
FUNCTION Album(loAlbum)

lcVerb = Request.GetHttpVerb()

*** Handle alternate verbs with same URL with
*** separate method since we don't have method overloading in VFP
IF (lcVerb == "POST" OR lcVerb == "PUT")
   RETURN THIS.SaveAlbum(loAlbum)
ENDIF

IF lcVerb == "DELETE"
   RETURN this.DeleteAlbum()
ENDIF   

lnId = VAL(Request.QueryString("id"))
IF (lnId < 1)
   ERROR "Invalid Id passed"
ENDIF

loBusAlbum = CREATEOBJECT("cAlbum")
IF (!loBusAlbum.Load(lnId))
   ERROR loBusAlbum.cErrorMsg
ENDIF

RETURN loBusAlbum.oData
ENDFUNC
*   Album

************************************************************************
*  SaveAlbum
****************************************
FUNCTION SaveAlbum(loAlbum)
LOCAL lnId, album, loBusAlbum

IF VARTYPE(loAlbum) # "O"
   ERROR "No album provided to save."
ENDIF   

lnId = loAlbum.pk

loBusAlbum = CREATEOBJECT("cAlbum")

IF (lnId < 1 OR !loBusAlbum.Load(lnId))
   *** Create new instance
   loBusAlbum.New()
   lnId = loBusAlbum.oData.Pk
ENDIF

loBusAlbum.oData = loAlbum
loBusAlbum.oData.Pk = lnId

IF !loBusAlbum.Validate()
   ERROR loBusAlbum.cErrorMsg
ENDIF

IF !loBusAlbum.Save()
   ERROR "Unable to save album"
ENDIF

*** Return the album with update data
RETURN loBusAlbum.oData
ENDFUNC
*   SaveAlbum

************************************************************************
*  DeleteAlbum
****************************************
FUNCTION DeleteAlbum()

lnId = VAL(Request.QueryString("id"))

loBusAlbum = CREATEOBJECT("cAlbum")
IF !loBusAlbum.Delete(lnId)
   ERROR loBusAlbum.cErrorMsg
ENDIF

RETURN .T.
ENDFUNC
*   DeleteAlbum

ENDDEFINE
```