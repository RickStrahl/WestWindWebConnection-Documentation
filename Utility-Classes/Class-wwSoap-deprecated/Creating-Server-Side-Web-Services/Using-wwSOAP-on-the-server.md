wwSOAP includes several methods that are used to facilitate creating output from a Web request quickly and easily. The following should work in any Web Development tool, but the examples here are using Web Connection's Response object for output.

### Creating a generic Web Service Process Method
Although Web Connection includes a generic handler for Web Serivces you can also insert Web Service functionality into any existing Process classes by manually handling the SOAP request. Do this allows you additional control over how the SOAP request is handled including the ability to check for custom headers and other important security checks you might have to perform.

You can create a Web Service in an existing Web Connection Process class with just a few lines of code:

```foxpro
FUNCTION SoapHelloWorld

* ** Extract the method name from HTTP header
lcSoapAction = Request.GetExtraHeader("HTTP_SOAPMETHODNAME")
lcMethod = EXTRACT(lcSoapAction,"#","|",,.T.)

loSOAP = CREATEOBJECT("wwSoap")

* ** Grab the parameters the client passed
DIMENSION laParameters[1,3]
lnParms = loSOAP.ParseSoapParameters(Request.FormXML(),@laParameters)

IF lnParms > 0
  lcName = laParameters[1,2]
ELSE
  lcName = "Unknown"
ENDIF

* ** This is our result value
lcResultValue = "Hello World, " + lcName + ". Time: " + TIME()

Response.ContentTypeHeader("text/xml")
Response.Write( loSOAP.CreateSOAPREsponseXML(lcMethod,lcResultValue) )
 
ENDFUNC
```

Note that you can return non-string values in the call to CreateSOAPResponseXML() and if you're using wwSOAP clients the value will be returned in the proper type.