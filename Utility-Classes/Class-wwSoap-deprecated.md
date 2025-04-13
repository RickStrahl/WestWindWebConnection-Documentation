> This class has been deprecated. For an automated way to call Web Services consider using .NET or the <a href="https://west-wind.com/wsdlgenerator/" target="top">West Wind Web Service Proxy Generator</a>

The wwSOAP class provides an easy way to call SOAP based Web Services from Visual FoxPro from Visual FoxPro code using an all Visual FoxPro implementation. It also provides the server side parsing tools so you can create a SOAP Server with any FoxPro based tool with just a few lines of code. In fact the Web Connection wwWebService class that provides the Web Connection Web Service interface does just that.

Athough Microsoft has the MSSOAP toolkit available there are a few issues with this product - namely that can't deal with complex data hardly at all. wwSOAP does a bunch of things that the MS Toolkit does not:<ul>
* Works on non-WSDL Web Services
* Can return typed results
* Supports some Complex objects for inbound parameters and outbound results
* XML DOM results for all other results
* Low level methods that allow custom result parsing for objects/arrays etc.
* Provides a SOAP proxy client class
* Provides WSDL generation and Proxy Class generation code
* Doesn't require the SOAP Toolkit on installations

The real strong points of this class are its ability to create XML from objects to be used for sending in the SOAP packet and turning returned XML back into VFP objects. Even if you do use the SOAP Toolkit the methods of this class can greatly help you in getting your data conversions taken care of much easier.

### Dependencies:
The following files are required in order to use wwSOAP in Visual FoxPro* wwUtils.prg
* wwHTTP.prg
* wwXML.vcx/vct
*  wconnect.h

<hr>
**Based on:** Relation
**Stored in:** wwSOAP.prg
<hr></ul>