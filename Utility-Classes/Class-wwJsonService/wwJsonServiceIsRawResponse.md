Allows the called method to specify that the result from the method should not be serialized. Used by wwRestService to not try set a content type or serialize and send out the result.

This is useful to allow a method to directly produce output for non-JSON results. For example if you have a RestService and decide to mix-in a couple images or document or even HTML requests.

This property can be accessed from within a service execution method via the PRIVATE JsonService instance as JsonService.RawResponse. 

For example this method inside of a JsonService returns HTML:

```foxpro
FUNCTION Index()

*** We take over the Response here - no JSON Serialization
JsonService.IsRawResponse = .T.

Response.ContentType = "text/html"
Response.ExpandTemplate(this.oConfig.cHtmlPagePath + "index.wcs")
Response.End()
ENDFUNC

FUNCTION Image(Id)

*** Load image from db
loBusImage = CREATEOBJECT("wwBusImage")
IF !loBusImage.Load(id) 
   ERROR loBusImage.cErrorMsg
ENDIF

JsonService.IsRawResponse = .T.
Response.ContentType = loBusImage.ContentType
Response.Write(loBusImage.ImageData)
Response.End()
ENDFUNC
```