Web Services can be created in several different ways. Web Connection includes a default Web Service Process class that handles any number of scripted Web Processes, or you can create a custom process class that can be implemented entirely via code in a process class. 

### Using the existing Web Service class to create scripted Web Services
Scripted Web Services can be created without having to change any code in the Web Connection installation. A scripted Web Service simply consists of a new page with a .wwSoap extension which is automatically handled by the Web Connection framework. You can also set up your own custom classes that use a different script map, but for now we'll deal with the default implementation.

To create a new Web Service use the Web Connection Management Console by typing `DO CONSOLE` and select *Create Web Service*.

![](IMAGES%5CMANAGEMENTCONSOLE%5CCREATEWEBSERVICE.GIF)

Enter the filename of the new Web Service and point it to a virtual directory on your Web site. Web Services are executed as PRG files, but are run dynamically through scriptmapping via the wwSOAP extension.

The scripted Web Service process consists of a small header program that is responsible for calling your actual implementation methods and a set of functions that make up the functionality of your Web Service. A basic Web Service with a Helloworld method looks like this:

```foxpro
* ** DO NOT REMOVE - CALL WRAPPER
PARAMETERS lcMethod, lcParmString, lvResult
PRIVATE _loServer
_loServer = CREATEOBJECT("SoapService")
lvResult =  Eval("_loServer."+ lcMethod + "(" + lcParmString+ ")" )
RETURN lvResult
ENDFUNC
* ** DO NOT REMOVE - CALL WRAPPER

* ** Class is not used OLEPUBLIC - OLEPUBLIC only used for SDL generation
DEFINE CLASS SoapService as RELATION OLEPUBLIC

FUNCTION helloworld(lcName as String) as String 
ltTime = DATETIME()
RETURN "Hello World, " + lcName + ". Good day " + TRANSFORM(ltTime) + "."
ENDFUNC

ENDDEFINE
```

The code at the top of the document must be added to every Web Service script you create - it's vital that this code is provided or the Web Service will not function correctly. This code is necessary so that the Web Connection framework can get to your Web Service and pass the call forward to the requested method. The OLEPUBLIC class will not actually be built or compiled into your project. It's here mainly to let you quickly build a COM object from your service so you can use the ROPE SDL Wizard to generate an SDL file and COM object as needed (you can use WC for debugging and then deploy the object either using Web Connection or ASP and the MS SOAP Toolkit- it works either way).

Note that to add functionality you simply add new methods with input parameters and a return value.

The above code implements a single function in the Web Service called HelloWorld. To call this method from a wwSOAP client use the following code:
```foxpro
oSoap = CREATEOBJECT("wwSoap")

oSoap.lIncludeDataTypes = .T.

oSoap.cServerUrl = "http://localhost/soap/soapservice.wwsoap"

oSoap.AddParameter("lcName","Rick Strahl")
lvResult =  oSOAP.CallMethod("HelloWorld")
? lvResult
? oSOap.cErrorMsg 
* ? oSoap.cRequestXML  && Debug
* ? oSoap.cResponseXML  && Debug
```


If you're using wwSOAP on the client you can also pass typed parameters and receive typed results. Lets add a method to the Web Service. Just open the .wwSOAP file and add a new method to the class:

<pre>FUNCTION addnumbers(lnValue as Integer, lnvalue2 as Integer) as Integer
RETURN lnValue + lnValue2
</pre>

You can immediately call this new method in your Web Service without recompiling or stopping the server. Note that the parameters and return value are numeric in this example:

```foxpro
oSoap = CREATEOBJECT("wwSoap")
oSoap.lIncludeDataTypes = .T.
oSoap.cServerUrl = "http://localhost/soap/soapservice.wwsoap"

oSoap.AddParameter(lcNumber1,10)
oSoap.AddParameter(lcNumber2,11)
lvResult =  oSOAP.CallMethod("addnumbers")
? lvResult   && 21.00
? VARTYPE(lvResult)
? oSoap.cErrorMsg
```

lvResult in this case comes back as a numeric value. Typed input is valid per SOAP spec, but be aware that MS SOAP does not use embedded type information instead relying on the external SDL file to get type information. Web Connection's Web Services support either embedded types or using an external SDL file if the types are not embedded.

### Creating a WSDL File
In order to use a Web Service properly from another client that isn't FoxPro, you'll want to have a WSDL 

**How scripted Web Services work**  
Web Services are compiled scripts and run as native VFP code. Depending on the wwServer::nScriptMode setting the scripts are either run pre-compiled (the FXP must exist and is never recompiled at runtime) or checked for the latest date and re-compiled on the fly if changed. Pre-Compiled scripts don't detect any changes made to the Web Service script code while the server is running - the files would have to manually compiled. Because .wwSOAP files are really just PRG files with a different extension you can simply do:

<pre>COMPILE \inetput\wwwrroot\wconnect\soapservice.wwSOAP</pre>

to compile it. You can also use the Admin page's Compile scripts option to compile *.wwsoap pages in a given directory via the HTML interface remotely.

When nScriptMode is set to Interpreted scripts the Web Serivce goes out to disk and compares dates between the FXP and the .wwSoap file. If the FXP doesn't exist or is older than the .wwSOAP file it recompiles it at runtime. This mode is obviously more flexible as it allows you to make changes and immediately see the changes online.

**Debugging scripts**  
Because scripts are real VFP programs you can debug them if running your Web Connection server inside of the VFP IDE. For example, you can simply SET STEP ON like this:

<pre>FUNCTION helloworld(lcName as String) as String 
ltTime = DATETIME()
SET STEP ON
RETURN "Hello World, " + lcName + ". Good day " + TRANSFORM(ltTime) + "."
ENDFUNC
</pre>

and the debugger will pop up in the middle of your Web Service.

**Error Handling**  
When the #DEFINE DEBUGMODE flag in wconnect.h is set to .T. scripts will stop on errors and bring up the code VFP editor to allow you to fix your code. With DEBUGMODE .F. (which you should always do with live servers!!!) the error will be passed back to the SOAP error handler which will create the appropriate SOAP faultcode XML response. Using this approach you can basically debug your Web services like any other VFP program.

### Process Class Method based Web Services
Script based Web Services are flexible because you can make changes to them interactively. However, you may prefer to use regular Web Connection request methods to create your Web Service methods. To create a Web Service based on a Process class you need to do the following:
<ul>
* Create a new WebServiceProcess class
You can copy the wwDefaultWebService class and save that file to a new name. It should look like this:
<pre>* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
* wwWebService :: wwWebService
* *** *** *** *** *** *** *** *** *** *** *** *** ***
* **  Function: Web Service Process Loader. Loads teh default
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
LPARAMETER loServer
LOCAL loProcess

#INCLUDE WCONNECT.H

loProcess=CREATE("wwYourWebService",loServer)

* ** Call the Process Method that handles the request
loProcess.Process()

RETURN

* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ***
DEFINE CLASS wwYourWebService AS WWC_WEBSERVICE
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ***

* Sample WebService Method - just add regular methods
FUNCTION TestWebService(lcName)
RETURN "Hello " + lcName + ". Time is: " + TRANSFORM(DATETIME())


ENDDEFINE
* EOC wwDemoWebService 
</pre>

* Add a new scriptmap if you plan to use both scripts and Process methods 
If you'd like to use a script map with your Web Service you have to set up a new one other than wwSoap so that the methods of your service can be found.

* Add methods to your process class for each Web Service function
Notice that all you need to do is add methods with input parameters and a return value
</ul>

### Testing your Web Services
To facilitate the process of testing your Web Services and as an example of how the wwSOAP client code works there's a sample form called SOAPDEMO that allows you to specify a method and  parameters to call. You can also load an SDL description and pick methods to call dynamically (demonstrating the type library aspect of SDL). You can find the demo in the TOOLS ddirectory and you can run it by typing:

<pre>DO FORM .\tools\soapdemo</pre>

This brings up an interactive form that allows you to specify a URL for a Web Service (any SOAP compliant URL really) a method and up to three parameters that you can visually add.

For example to access the demo Web Service provided try:

<blockquote>http://localhost/wconnect/soap/soapservice.wwsoap  
helloworld
lcName  Rick
</blockquote>

To explore the demo Web Service click on the arrow and enter:

http://localhost/wconnect/soap/soapservice.xml

into the SDL text box, then click Go. This should load all the methods of the Web Service and show them in the listbox. Select any item and the rest of the form will fill with the service URL, method name and the parameters and their types. You can run each request and check out the SOAP messages passed between client and server.
<hr>
For more complete information see our online white paper at:
<a href="http://www.west-wind.com/presentations/xmlmessaging/soapwebservices.htm" target="top">http://www.west-wind.com/presentations/xmlmessaging/soapwebservices.htm</a>