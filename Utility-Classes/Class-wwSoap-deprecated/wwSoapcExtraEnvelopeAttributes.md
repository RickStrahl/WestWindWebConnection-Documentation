String value that can be added to the SOAP Envelope root envelope. Useful to add namespace references to the SOAP envelope required by the soap body if you are manually creating parameter or the entire soap envelope.

For example if you have a service that requires a custom parameter that wwSOAP can't automatically parse (portNames)

```foxpro
TEXT TO cPortNames NOSHOW
<portNames q0:arrayType="xsd:string[2]"
          xsi:type="q0:Array">
	<string xsi:type="xsd:string">Busan</string>
	<string xsi:type="xsd:string">London</string>
</portNames>
ENDTEXT

LOCAL loSoap as wwSoap
loSoap = CREATEOBJECT("wwSoap")
loSoap.cExtraEnvelopeAttributes = ;
	[ xmlns:q0="http://schemas.xmlsoap.org/soap/encoding/" ] +;
	[ xmlns:xsd="http://www.w3.org/2001/XMLSchema" ] +;
	[xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"] 
loSDL = loSoap.Parseservicewsdl("http://dist.netpas.net/NPSystem/services/NPDistance?wsdl",.t.)

loSoap.AddParameter("pinCode","DEMO")
loSoap.AddParameter("accessCode","DEMO")
loSoap.AddParameter("portNames",cPortNames,"RawXml")

result = loSoap.CallWsdlMethod("getDistance",loSdl)
```