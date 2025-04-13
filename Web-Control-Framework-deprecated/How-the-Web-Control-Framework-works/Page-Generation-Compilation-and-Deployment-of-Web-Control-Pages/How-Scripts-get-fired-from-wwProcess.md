Web Connection 5.0 integrates Web Control processing right into the core framework. wwProcess works by default looking for a matching method to execute based on the page name. In Version 5.0 the framework has been extended so if a method cannot be found it will try to execute the physical page that it maps to on disk. The default execution mode is to process the page as Web Control page

There's a flag on the wwProcess class - nPageScriptMode that determines this operation and the default is 2 which is to execute the page as Web Control Page. You can also use a value of 1 which is the 'legacy' behavior that uses ExpandTemplate instead.

### The default extension: WCSX Pages 
Web Connection integrates the Web Control Framework into the standard Web Connection wwProcess class processing that is used to route requests. By default Web Connection and any new project created through the Wizards is already configured to process any page that ends in WCSX which is routed to the generic wwWebPageHandler class. This class is merely a subclass of wwProcess. This class is meant to be generic and not to be modified. 

This means that WCSX pages run out of the box, but you can't configure the process class and create common entry points for configuring each request. This may or may not be a problem.

### Creating custom Web Control Page extensions
However, Web Connection can hook Web Control pages to any wwProcess class and by default this functionality is enabled via nPageScriptMode = 2. If a method is not found, Web Connection will try to find the physical page and try to process it using the Web Control Page processing.

This means that you can use the same wwProcess based class as your base handler for both method based implementations AND use Web Control Pages. The advantage of this approach is that you can customize the startup code and have a common hook point for your wwProcess class using OnProcessInit or Process and you can add properties and methods to this class that will be visible via the *Process* reference in the script code.

The whole Process class looks like this:

```foxpro
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
* PROCEDURE wwPageDemo
* *** *** *** *** *** *** *** *** ***
* **  Function: Processes incoming Web Requests for wwPageDemo
* **            requests. This function is called from the wwServer 
* **            process.
* **      Pass: loServer -   wwServer object reference
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ***
LPARAMETER loServer
LOCAL loProcess
#INCLUDE WCONNECT.H

LOCAL loProcess 
loProcess=CREATE("wwPageDemo",loServer)
loProcess.lShowRequestData = loServer.lShowRequestData

IF VARTYPE(loProcess)#"O"
   * ** All we can do is return...
   WAIT WINDOW NOWAIT "Unable to create Process object..."
   RETURN .F.
ENDIF

* ** Call the Process Method that handles the request
loProcess.Process()

RETURN

* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ***
DEFINE CLASS wwPageDemo AS WWC_PROCESS
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ***

nPageScriptMode = 2   && Web Control Pages

* ** Fires for both methods and Web Control pages.
FUNCTION OnProcessInit
THIS.oResponse.cStyleSheet = this.ResolveUrl("~/webcontrols/westwind.css")
ENDFUNC

* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
FUNCTION HelloWorld()
* *** *** *** *** *** *** *** **
THIS.StandardPage("Hello World from wwPageDemo process",;
                  "If you got here, everything should be working fine.<p>" + ;
                  "Time: <b>" + TIME()+ "</b>")
                  
ENDFUNC
* EOF wwPageDemo::HelloWorld

* ** Recommend you override the following methods:

* ** ErrorMsg
* ** StandardPage
* ** Error

ENDDEFINE
```

For example, let's say you have a Web Store application, that has an extension of WWS. By default it manages requests mapped to methods. But if a page called SomeNonMethodPage.wws is called it's then routed directly to the Page processing framework.

To configure this setup you'll need to configure Visual Studio to make sure that your extension is recognized:
<ul>
* Map the Web Form editor to the .wws extension
* Set up a Provider mapping in web.config to this extension
</ul>

Thiese are outlined in [Configuring VS.NET 2005](vfps://Topic/_1LO02GGFG).

Once this is in place you can now instantiate your demo simply by going to the name of the page.

<a href="http://localhost/wconnect/webcontrols/firstForm.wws" target="top">http://localhost/wconnect/webcontrols/firstForm.wws</a>