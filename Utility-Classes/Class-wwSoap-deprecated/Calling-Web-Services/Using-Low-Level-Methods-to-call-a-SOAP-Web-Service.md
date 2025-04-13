CallMethod() is a high level method that handles the entire process of packaging the SOAP request and then calling the Web Service over HTTP. It handles just about everything you need. But if you need more control over packaging your SOAP method calls and retrieve multiple result values (OUT and IN/OUT parameters in addition to a return value) you can use the slightly lower level methods:

<pre>oSoap = CREATE('wwSoap')
oSoap.cServerUrl = 'http://www.west-wind.com/soapservice.wwSoap'  && Any SOAP url

oSoap.AddParameter('lcName','rick')
oSoap.AddParameter('lnValue',10)
oSoap.AddParameter('ltDate',DATETIME())

* ** Create SOAP request XML
lcRequest = oSoap.CreateSoapRequestXML('helloworld')

MESSAGEBOX(o.cRequestXML)  && lcRequest

* ** Call the server with Soap Request
lcResponse = oSoap.CallSoapServer()
IF oSoap.lError
   MESSAGEBOX(oSoap.cResponseXML,48,'ERror')
   RETURN
ENDIF   
   
MESSAGEBOX(lcResponse)  && oSoap.cResponseXML

lvResult = oSoap.ParseSoapResponse(lcResponse)
? lvResult
? VARTYPE(lvResult)

* ** Retrieve multiple IN/OUT parameters if any
FOR x=2 to  oSoap.nReturnValueCount
  ? oSoap.aReturnValues[x,2]
ENDFOR
</pre>

### Using SOAP Messages for other purposes
You can also use SOAP messages for other purposes such as passing messages between systems in Asynchronouse messaging using the wwAsyncWebRequest class for example. In these cases you'd want to create and parse SOAP messages manually. The following code demonstrates how to create a SOAP message manually and then parse it, process it and send an appropriate SOAP response:

<pre>oSOAP = CREATEOBJECT("wwSOAP")

oSOAP.AddParameter("lcName","Rick")
oSOAP.Addparameter("lnValue",10)

* ** Create SOAP Request XML
lcSoapRequest = oSOAP.CreateSoapRequestXML("TestMethod")
? lcSOAPRequest

* ** Parse over all parameters
DIMENSION laParms[1]
lnParms = oSOAP.ParseSoapParameters(lcSOAPRequest,@laParms)
FOR x = 1 TO lnParms
   ? laParms[x,1],laParms[x,2],laParms[x,3]
ENDFOR

* ** Or get a named parameter explicitly
lcResult = "Hello " + oSOAP.GetNamedParameter("lcName") + ". You passed me: " + ;
           TRANSFORM( oSOAP.GetNamedParameter("lnValue") )

* ** Create a SOAP response to send back
lcSOAPResponse = oSOAP.CreateSoapResponseXML("TestMethod",lcResult)
?
? lcSOAPResponse
</pre>