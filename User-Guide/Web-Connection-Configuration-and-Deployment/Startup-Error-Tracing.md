When Web Connection starts up your application in a non-debug compiled EXE server, error handling is not initially available as the server initializes. That's because the server initializes as part of an `OnInit()` sequence and if that fails, well your server will never actually become live.

### Separated OnInit and OnLoad Sequence
In Web Connection 6+ the startup sequence has been minimized with a shortened `OnInit()` cycle, and a delayed `OnLoad` handler call that fires only on the first hit to your server. This reduces the potential failure scenarios that can occur if your server fails before it is fully instantiated. Errors can still occur but they are now a little bit easier to debug.

### Startup Failures: Module Logging in wcErrors.txt
If the server fails to initialize completely, any errors are logged by the Web Connection Handler (.NET or ISAPI) in `wcErrors.txt` in the temp folder for the application. Startup errors logged there will include DCOM permissions errors, invalid Class IDs for your COM server, missing files or runtimes or any failure that causes the server to crash during `OnInit()`

> #### @icon-info-circle Avoid putting code into `OnInit()`
> To keep startup code to an absolute minimum, avoid writing code in your server's `OnInit()` method. `OnInit()` is meant to only set essential server operation settings that are needed for Web Connection servers to start. For everything else that needs to initialize use `OnLoad()`. In typical scenarios you shouldn't have any code in `OnInit()` beyond the generated default. This alone should avoid server startup crashes due to FoxPro code errors.

### Startup Errors are logged to wcTraceLog.txt
Any code based errors during startup are logged to `wcTracelog.txt` file which is hooked into the `OnInit()` and `OnLoad()` processing of your server. Both methods are wrapped into exception handlers and if they are triggered by errors `wcTraceLog.txt` receives the error information. You can also implement `OnError()` to receive the captured exception and log or otherwise take action.

> #### @info-icon-circle Folder Permissions for Logging
> Make sure that the folder your application's EXE is running out of has read/write access rights for the IIS Server account that is running FoxPro application as it needs to be able  to create and write the `wcTracelog.txt` file.

Any failures in `OnInit()` cause the server to not start so `wcTracelog.txt` and `wcErrors.txt` will be your only error source. 

Errors in `OnLoad()` log to `wcTracelog.txt` but also display an error page in the browser with the error information (WC 6.15+). If `OnLoad()` errors occur the server will run not any further and **only display the error message** - requests are aborted until the problem is fixed.

### Capturing Startup Errors
Beyond looking in `wcTraceLog.txt` you can also override the `wwServer::OnError()` method which receives the exception of the failure. In that message you can add custom logging and write out additional environment info into the log file.

You can also use the `wwServer::Trace()` method to write out information into the `wcTraceLog.txt` log. For thorny problems this allows to put messages into your code to see how far it gets and echo state that might help you debug the application. It's also useful in requests, but it's especially valuable for debugging startup errors.

The OnError method only serves as an additional error logging mechanism that allows you to capture the error and possibly take action on the error with custom code.

To implement:

```foxpro
FUNCTION OnError(loException)

*** default logging and some cleanup
DoDefault(loException)

*** Do something with the error

'*** Also write out to the local trace text log
THIS.Trace(loException.Message)

ENDFUNC
```
### Add Tracing and Logging Into your Code
Finally if all of this still hasn't fixed your server  to start up, you'll have to do some detective work. Your first line of defensive is always debug locally first in a similar environment: Make sure you debug in COM mode locally so you get as close as possible to the live environment.

If you really have to debug the live server you can use the [wwServer::Trace()](vfps://Topic/_4AX0U7IQ5) method to quickly write out trace messages to the `wcTraceLog.txt` file.

```foxpro
PROTECTED FUNCTION OnLoad

THIS.Trace("OnLoad Started")

THIS.InitializeDataBase()
THIS.Trace("DataBase Initialized")

THIS.SetLibraries()
THIS.Trace("Libiraries loaded")

...

THIS.Trace("OnLoad Completed")
ENDFUNC
```

By default the `wwServer::Trace()` method stores simple string output with a date stamp in `wcTraceLog.txt` in the application's startup folder.

Using this type of Print-Line style output you can put trace points in key parts of your startup sequence to see whether code is reached and what values are set.

### Common Startup Errors
Common startup errors include:

#### Invalid COM Object Configuration  
Make sure your servers are listed properly in `web.config` (.NET ) or `wc.ini` (ISAPI) and point at the right ProgIds for your COM servers. Also make sure the COM Servers are registered.

#### Folder Locations  
Make sure that your application can run out of the deployed folder and has access to all the locations that it needs to read local data from. Make sure that paths are set in the environment and network drives are connected and so forth. **Servers don't run under the interactive account** so don't expect the same permissions and environment as your loggd in account especially if you depend on mapped drives - you probably have to map drives as part of your startup routine by checking if a drive is mapped and if not mapping it. Use `SET PATH TO <path> ADDITIVE` or set the system path to include  needed folders.

#### Folder Permissions
Make sure that any files including data files you access on the local file system have the right permissions so they can be read and written to. Remember the IIS or DCOM permissions determine what account your application is running under.