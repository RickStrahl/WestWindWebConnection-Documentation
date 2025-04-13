Goto [Step 1](vfps://Topic/Step%201%20-%20Pick%20the%20project%20and%20process)
<hr>
The new process wizard adds a new process class to an existing application. It does so by adding some code into a main startup file (WebDemoMain.prg) for example, and hooking it to a new PRG file containing the new process class template.

The Wizard also allows you to configure a scriptmap that maps to this sub-application. This allows for script based request routing where the extension (.wpfor WebProcess) is always routed to the new class and the page name (Helloworld.wp goes to WebProcess::HelloWorld). The default wwProcess handler makes this type of request routing automatic.

### Requirements
Web Connection's code insertion requires a specifc format in the main program files in order to be able to insert new process class hooks into the main program file. If the project was generated using the new project wizard or you're using the wcDemoMain.prg file the this will already be the case. If you're using an older version of Web Connection you will have to add the following lines in the your server's main Process class (WebDemo::Process for example):


```foxpro
 DO CASE

      CASE lcParameter == "WEBPROCESS"
         DO WebProcess with THIS

<span style="Color:Blue">      * **SUB APPLETS ADDED ABOVE - DO NOT MOVE THIS LINE * **</span>

      CASE lcParameter == "WWMAINT"
	      DO wwMaint with  THIS
  OTHERWISE
     * **Check for Script Mapped files for: .WC, .WCS, .FXP
     lcPhysicalPath=THIS.oRequest.GetPhysicalPath()
     lcExtension = Upper(JustExt(lcPhysicalPath))

     DO CASE

     CASE lcExtension == "WP"
        DO WebProcess with THIS

<span style="Color:Blue">     * **ADD SCRIPTMAP EXTENSIONS ABOVE - DO NOT MOVE THIS LINE * **</span>

     * **Default Web Connection handling
     CASE lcExtension == "WC" OR lcExtension == "FXP"
        DO wwScriptMaps with THIS
        ...
     ENDCASE

     ...
  ENDCASE
```

Once these comments are in the document, the wizard will insert routing code above those lines for the plain parameters (wc.dll?webDemo~HelloWorld) as well as script mapped parsing (HelloWorld.wp).

<blockquote>
*Note you should also add any SET PROCEDURE,CLASSLIB, PATH and other commands that you expect to require in your custom code to the SetServerProperties method.
*</blockquote>

## Code Generation
The Wizard relies on templates for generating the actual code of your classes and INI file settings. These templates can be found in the .\templates directory off the Web Connection root. The Process.prg template is used to generate a new Process class, which gets initialized to the name that you specify for the process class. You may customize these templates for your own needs although I recommend to stick to the minimal approach that is provided by default.

The process class generated looks as follows:

```foxpro
* ***********************************************************************
*PROCEDURE WebProcess
* ***************************
LPARAMETER loServer
LOCAL loProcess
#INCLUDE WCONNECT.H

loProcess=CREATE("WebProcess",loServer)

IF VARTYPE(loProcess)#"O"
   * **All we can do is return...
   WAIT WINDOW NOWAIT "Unable to create Process object..."
   RETURN .F.
ENDIF

* **Call the Process Method that handles the request
loProcess.Process()

RETURN

* ************************************************************
DEFINE CLASS WebProcess AS WWC_PROCESS
* ************************************************************

*********************************************************************
* Function  OnProcessInit
************************************
*** If you need to hook up generic functionality that occurs on
*** every hit against this process class , implement this method.
*********************************************************************
FUNCTION OnProcessInit
LOCAL lcScriptName, llForceLogin

*** THIS.InitSession("MyApp")
*** 
*** lcScriptName = LOWER(JUSTFNAME(Request.GetPhysicalPath()))
*** llIgnoreLoginRequest = INLIST(lcScriptName,"default","login","logout")

*** IF !THIS.Authenticate("any","",llIgnoreLoginRequest) 
***    IF !llIgnoreLoginRequest
*** 	  RETURN .F.
***   ENDIF
*** ENDIF

*** Explicitly specify that pages should encode to UTF-8 
*** Assume all form and query request data is UTF-8
Response.Encoding = "UTF8"
Request.lUtf8Encoding = .T.

RETURN .T.
ENDFUNC


* ********************************************************************
FUNCTION TestPage()
* ***********************

THIS.StandardPage("Hello World from the WebProcess process",;
                  "If you got here, everything should be working fine")
                  
ENDFUNC
* EOF WebProcess::HelloWorld


* **Recommend you override the following methods:

* **ErrorMsg
* **StandardPage
* **Error

ENDDEFINE</pre>
```

When you generate this process class you should be able to immediately access it with either:

* Helloworld.wp (recommended - always use script map!)
* wc.wc?WebProcess~HelloWorld


Looking at the code you can see that the Process method is generated but commented out. By default the wwProcess's Process method is used to handle all request processing. You should uncomment the custom process code if you need to provide any generic handling that needs to occur on every hit as the Process method is called before any other method in the class fires from a Web hit. This method is perfect for handling things like authentication, cookie checks and assignment, logging etc. Make sure that if you implement this method you add a call to **DoDefault**to call the default behavior, which is responsible for routing the request to the appropriate method of the class (like HelloWorld).