Although Web Connection provides full support for Web Service classes you can also use the wwSOAP class to build individual SOAP handler methods easily by grabbing input parameters and creating output with just a few lines of code. 

This functionality is adaptable to any Web Development tool using Visual FoxPro and doesn't require Web Connection explicitly. All you need is to be able to retrieve the raw HTTP POST buffer and be able to write output back into the HTTP stream. The following example uses Web Connection and the two methods used to handle input and output are Request.FormXML() (retrieves the raw HTTP post buffer containing SOAP XML request) and Response.Write() (output the resulting SOAP response XML).

```foxpro
FUNCTION ManualSoapDemo

loSOAP = CREATEOBJECT("wwSOAP")

* ** We'll always write XML output
Response.ContentTypeHeader("text/xml")

lcSoapRequest = Request.FormXML()
IF lcSoapRequest  # "<?xml" 
   Response.Write(loSoap.SOAPErrorResponse("Invalid XML posted")
   RETURN
ENDIF 

* ** Retrieve Input Parameters
DIMENSION laParameters[1,3]
lnParms = loSOAP.ParseSoapParameters(lcSoapRequest,@laParameters)

IF lnParms # 1
   Response.Write(loSoap.SOAPErrorResponse("Invalid number of parameters passed"))
   RETURN
ENDIF   

* ** Retrieve the parameter
* lcName = loSOAP.GetNamedParameter("lcName")
lcName = laParameters[1,2]

lcResultValue = "Hello World, " + lcName + ". Time: " + TIME()

* ** Return the SOAP response for this method
* ** Note this result will be typed by the value of the result
Response.Write( loSOAP.CreateSOAPREsponseXML(loSoap.cMethod,lcResultValue) )
 
ENDFUNC
```

wwSOAP also supports multiple return values, but unless you use some sort of Service Contract it won't know whether parameters were passed by reference. You can however return multiple values on custom requests easily by using code like the following to generate the response XML:

```foxpro
loSoap.AddReturnValue("return",lnResultValue)
loSoap.AddReturnValue("ByRefValue1",lcParm1)
loSoap.AddReturnValue("ByRefValue2",lcParm1)

* ** No value parameter!
lcXML = loSoap.CreateSoapResponseXML(loSOAP.cMethod)
```