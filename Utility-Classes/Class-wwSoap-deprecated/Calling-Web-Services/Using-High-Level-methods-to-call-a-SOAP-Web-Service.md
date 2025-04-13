When talking about Web Services we generally talk about SOAP and WSDL based services. WSDL is a SOAP related standard that is used to describe the functionality of a Web Service. It acts as a sort of a Type Library for a Web Service and provides type information for parameters and return values. Some SOAP implementations especially MSSOAP and .Net based Web Services require WSDL files in order to call Web Services.

To call a Web Service that has a WSDL definition available you can use the following code:

```foxpro
DO WWSOAP
lcWSDL = "http://www.foxcentral.net/foxcentral.wsdl"

loSOAP = CREATEOBJECT("wwSOAP")

loSOAP.ParseServiceWSDL(lcWSDL)
loSOAP.AddParameter("lnDays",60)
loSOAP.AddParameter("lnProvider",0)
loSOAP.AddParameter("lcType","All")

lcXML=loSOAP.CallWSDLMethod("GetNewsItems")

IF loSOAP.lError
   ? loSoap.cErrorMsg
   RETURN
ENDIF

XMLTOCURSOR(lcXML,"TNewsItems")

BROWSE
```

You start by telling wwSOAP which WSDL file (or link) to work with, which parses the methods and objects of the service. Next the actual call to the Web Service is made with the parameters you specify. AddParameter is pretty flexible in how you can pass parameters including the ability to explicitly coerce types. You then call the CallWSDLMethod method with the actual name of the method to call and off it goes.

You should always check lError on return. Any failures - whether at the connection or server application or client parsing error will be captured and returned in wwSoap's error methods so you have a single point for checking them.

The call returns a result value - in this case an XML string is returned which is then turned back into a cursor for browsing...