Can be used to launch the Visual Studio Web Server (webserver.webdev.exe in the .NET 2.0 Framework dir) or the Web Connection Cassini Web Server for use with the Web Connection Managed module. Cassini is similar to the Visual Studio server but can be redistributed with your applications for local Web server operation.

Note this feature is also available via command the CONSOLE:
```foxpro
DO console WITH "LAUNCHVSWEBSERVER","c:\westwind\wconnect\webcontrols","81","/webcontrols"
```

or from the Windows Command Prompt:
<pre>console "LAUNCHCASSINI" "c:\westwind\wconnect\webcontrols" 81 "/webcontrols"</pre>

Please be sure to read the startup and configuration sections - both servers require [startup configuration](vfps://Topic/_2611D4RBH).