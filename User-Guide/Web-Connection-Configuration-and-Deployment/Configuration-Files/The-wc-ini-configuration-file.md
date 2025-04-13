The wc.ini file contains settings that determine the operation of the Web Connection ISAPI interface. The values set in the INI file affect the operation of the low level interface that runs inside of IIS under the security context configured for IIS or the specific IIS Application Pool that hosts the ISAPI DLL.

> #### @icon-warning ISAPI Only Settings
> Note the wc.ini file applies to operation with the ISAPI extension (wc.dll). When operating with the Web Connection .NET Managed Handler the configuration file settings are stored in `web.config` in the root of your Web application.

This file contains several runtime options that are used by the ISAPI DLL to determine how to operate. A typical wc.ini file looks like this:

```ini
[wwcgi]
;*** THIS DIRECTORY MUST HAVE READ AND WRITE ACCESS FOR THE
Path=C:\WebConnectionProjects\WebDemo\Deploy\Temp

;*** Message File Template (1st 3 letters)
;*** Default is "wc_"          Only needed if using a different template
Template=WC_

;*** Messaging Mechanism  of the DLL:    REQUIRED
;*** File       -   Original Web Connection Logic of 
;                   file based messaging
;*** Automation  -  Use OLE Automation Server Interface
;*** Interactive -  Call up the VFP development environment via Automation
Mechanism=File

;*** Time to allow request to finish
;*** Process will be terminated after number of secs
;*** specified here.           REQUIRED 
Timeout=60

;*** Specify how often wc.dll polls for 'completion' message
;*** Specify in milliseconds.  REQUIRED FOR FILE BASED
PollTime=100

;*** Limits the size of data posted to the Web Server
;*** In bytes. 0 means allow any size. Note: Oversize
;*** buffers result in Server 500 errors.
PostBufferLimit=0


;*** Account for Admin tasks    REQUIRED FOR ADMIN TASKS
;***       NT User Account   -  The specified user must log in
;***       Any               -  Any logged in user
;***                         -  Blank - no Authentication
AdminAccount=ANY

;Admin Page that is used for Backlinks from various internal pages
;Use a full server relative Web path!
AdminPage=/WebDemo/admin/Admin.aspx

;*** You can update an EXE on the fly from the UpdateFile
;*** With File base messaging you can also use StartEXE to start the
;*** ExeFile running
ExeFile=C:\WebConnectionProjects\WebDemo\Deploy\Webdemo.exe
UpdateFile=C:\WebConnectionProjects\WebDemo\Deploy\TempWebdemo.exe
FileStartInstances=0

;*** Determines whether the ISAPI DLL logs every request
;*** by writing a start and complete entry in wcErrors.txt
;***    0  -   No Logging (Errors are still logged though)
;***    1  -   Start and Complete logging
LogDetail=0

;*** If set double checks for a RequestId HTTP header on
;*** on return and matches the inbound request Id with the
;*** outbound request id
;***      0   -   Don't validate
;***      1   -   Validate and Show Error on failure
;***      2   -   Validate and Kill Application Pool on failure
ValidateRequestId=1

[Extra Server Variables]
;Var1=LOCAL_ADDR
;Var2=APPL_MD_PATH

[Automation Servers]
;*** 0 - Load Based, 1 - Round Robin
ServerLoading=1

;*** Specify as many servers as you want to load
Server1=Webdemo.WebdemoServer
;Server2=Webdemo.WebdemoServer
;Server3=Webdemo.WebdemoServer

;*** Determines whether CoInitialize for COM objects
;*** Set this option to 1 only if your servers do not
;*** load and given an error message to the effect 
;*** that COM is not initialized. Should only be needed
;*** on ancient or non-Microsoft Web Servers.
CallCoInitialize=0

[HTML PAGES]
Busy=
NoOutput=
OleError=
;*** Use these to override DLL messages
;Maintenance=c:\somepath\somepage.htm
Execption=
Maintainance=
NoLoad=
PostBufferSize=
```


**Path and Template**  
These two settings determine the File based temp file path. The `Path` describes the folder where temp files are store and the `Template` is the file prefix used for the temp files created. Using the `Template` allows you to run multiple applications into the same temp folder with different prefixes.

** Mechanism**   
Determines which mode the Web Connection module runs in:

* File
* COM

File based messaging uses temporary files on disk to communicate between the Web Connection server and the IIS module. COM uses COM objects to push that same information to the application.

**Timeout**  
Determines the request timeout in seconds before the module considers the request as timed out and returns a failure response to the client.

**PollTime**  
The timer interval used by the IIS module in File Based messaging to poll for completion of request data. The server checks for matching files and if none are available waits for the poll timeout before checking again.

**PostBufferLimit**  
Determines the max size in bytes of a POST/PUT operation from the client. Use this to limit the size of uploads. 0 means any size. We recommend you **don't** set this value here and instead set the value at the Web Server level (in web.config|system.web|httpRuntime|maxRequestLength).

**AdminAccount**  
This value determines what account(s) have access to the IIS Module admin pages. This can be a comma delimited list of Windows usernames, or a value of `ANY` which means any logged in user has access. This value can also be blank, which means no authentication occurs (NOT RECOMMENDED!)

**AdminPage**  
Link to the Admin page - used by some of the module pages to link back to the Admin page.

**LogDetail**  
Determines the detail level of the logs. 0 is the default level that logs errors, and 1 is verbose which logs startup, shutdown code and per request data. Use verbose mode when you need to debug server problems.

**ValidateRequestId**  
When set to 1 creates a unique request id for each request and passes it to the Web Connection server, which has to return the same request id. If the id doesn't match the request is failed. Used in the ISAPI extension to prevent any potential session cross talk. Errors are logged into the log file.


**Extra Server Variables**
The Web Connection ISAPI extension retrieves a fixed set of server variables and this section lets you add additional potentially new server variables that you want to retrieve on all requests. Use key values of var1,var2,var3 to specify the key of additional IIS server variables that you want to retrieve as part of your request. 

**Automation Servers**  
Use Server1,Server2,Server3 to specify the COM ProgId of each server to load. If you want two servers to load specify Server1 and Server2. For 3 add Server3 and so on. **ServerLoading** determines whether requests are balanced load based (1) which uses the first available server instance in order or round robin (2) which rotates through all servers.

**Html Pages**  
The Web Connection ISAPI extension can throw several errors internally. By default these errors generate error messages that are displayed as HTML generated to a simple template inside of the DLL. Since these errors never hit the Web Connection Visual FoxPro server, this means you can't change the error message directly. Some of these errors can be overrridden using the `[Html Pages]` section in the wc.ini file.

```ini
[HTML PAGES]
DLLStatusHeaderText=Custom Web Connection Status DLL Text
Exception=C:\westwind\wconnect\error.htm
NoOutput=C:\westwind\wconnect\error.htm
Maintenance=C:\westwind\wconnect\error.htm
Maintenance=C:\westwind\wconnect\error.htm
NoLoad=C:\westwind\wconnect\error.htm
Busy=C:\westwind\wconnect\error.htm
OleError=C:\westwind\wconnect\error.htm
Timeout=C:\westwind\wconnect\error.htm
```

In some instances this may not be appropriate. While errors are rare and usually point to a problem in the Visual FoxPro server code, it's sometimes necessary to provide an error message that is suitable for end users rather than the technical message that the DLL pops up. So, to do this you can provide an override form for each error message via settings inside of wc.ini. The following entries and error messages are available for customization: Ini Entry [HTML PAGES] Conditions:

* **Exception**  
An exception occurred during Web Connection processing. This is a wc.dll internal error and should hopefully never occur.  

* **NoOutput**  
The Web Connection server returned no output to the ISAPI extension. This means your Web Connection VFP code failed to create any output, which should never happen.  

* **Maintenance**  
Your server is set into Maintenance mode and the HoldRequest flag is set to 1, which doesn't allow requests to reach your VFP server. Use this for maintenance while the server is running. Users will see a message that says the server is in Maintenance mode.  

*  **NoLoad**  
The Web Connection servers couldn't be loaded (Automation only)  

* **Busy**  
All server instances are busy (Automation only)  

*   **OleError**  
An exception occurred while calling the Automation server.  

*   **Timeout**  
The request has timed out.  

*   **PostBufferSize**  
Displayed when the incoming POST buffer exceeds PostBufferSize key  

*   **TransmitFileFailure**   
Occurs when TransmitFile can't access or process the file to send.  

*   **InvalidRequestId**  
Occurs when the RequestId returned in the returned HTTP header does not match the inbound request Id.  

If entries are left blank the default error messages are brought up generated by the DLL. You can assign files to each of these keys that identify the page that you would like to load. Pages are loaded from the specified file - pages should not exceed 16k in size.