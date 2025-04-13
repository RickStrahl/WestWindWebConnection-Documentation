The SOAP spec makes no provision for an external description of the Web Service file. However, the MS SOAP implementation strictly relies on an external Service Description Language (WSDL) file to describe the methods inside of the SOAP Web Service. Both the MS SOAP client and server use this WSDL file to validate the method call.

Web Connection Web Services don't require a WSDL file - you can simply call the Web Service and since Web Connection supports data typing in the SOAP request and response, use of a WSDL file is not vital to operation of the Web Service. However, a Service Description is handy to discover type information about the Web Service and the SOAPMethodTester form can actually use this information if available to provide you with a method signature to call. 

In addition, Web Connection supports creation of a proxy class that is created from the WSDL definition of any Web Service. The generated class includes all the methods with the proper call signatures that the Web service provides. Using a class in this manner makes it possible to use Intellisense on the remote methods as well as providing an efficient wrapper mechanism that can cache WSDL file definitiions and more.

To create an WSDL file use the Management Console (DO CONSOLE) and select Create Web Service SDL file.

![](IMAGES%5CMANAGEMENTCONSOLE%5CWEBSERVICETOSDL.GIF)

Pick the Web Service file to create an SDL for and click OK. Web Connection will generate a COM DLL from the Web Service and then parse the type library into the SDL format. When done all intermediate files are deleted with the exception of:

<yourwebservice>.XML    - The SDL file
<yourwebservice>.DLL     - a multi-threaded DLL

The latter may not function properly unless the PRG file was self-contained and loaded all dependent PRGs and VCX files required for the project. The file is not deleted because it is locked until a CLEAR ALL, CLOSE ALL is issued. After that the DLL can be deleted.

### Generating a Web Service Proxy class
Once a Web Service exists on the Web it's nice to have a very easy way to call it. Although the code to call a Web Service with wwSOAP is relatively short (basically create the class add parameters and call the method) it's not super intutive - nothing like calling a class method directly. You also are not getting any help with what methods are actually availalbe and how to call them.

To facilitate this process Web Connection can generate a Proxy class for a Web Service for you based on a WSDL definition that describes the Web Service. To do so, select the URL of the WSDL file and pick a PRG filename that you want to generate the proxy class into.

### WSDL Generation Limitations
It's important to understand how the WSDL generation works with FoxPro. Basically the generator compiles your class into a COM object and then uses the COM type library to create the WSDL document from the type data available for methods and properties.

This works well as long as:

<ul>
* Your types are annotated with AS STRING, AS INTEGER, etc.
* Your types are simple Types
</ul>

#### No Complex Type Support
Unfortunately FoxPro doesn't have a meta data based type system so there's no compile time mechanism to generate type information on complex types. This means if you return objects or collections those types will not actually generate strong type information in the WSDL document.

Unfortunately this is a limitation of all FoxPro based solutions - due to the lack of type info there's no reliable way to generate a WsDL document. 

This is one of the reasons why we advocate using some other technology that can interface with FoxPro to create your Web Service. A good solution is to use .NET to act as the Web Service front-end with FoxPro providing the data backend either via OleDb data access or by using FoxPro COM objects.

The following article has more information on how to accomplish this:
**<a href="http://www.west-wind.com/presentations/foxdotnetwebservices/" target="top">Calling and Hosting FoxPro Web Services through .NET</a>**