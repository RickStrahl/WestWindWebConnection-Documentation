All FoxPro source files of the Internet Protocols and Client Tools can be directly compiled into your FoxPro application, so these files just become part of your application. Make sure you add the appropriate SET PROCEDURE and SET CLASSLIB commands and that's all it takes to activate the classes.

There are several DLL files which are optionally required for this library:

* wwIPStuff.dll - SMTP, Sockets, Pop3 and a few others (required)
* wwImaging.dll - Image manipulation functions (optional)
* zlib1.dll- Required only if you want to use GZip in wwHTTP (optional)
* wwDotNetBridge.dll - for any .NET Interop using wwDotNetBridge class (optional)

wwIPStuff is used for a number of features including some of the API routines so it's a good idea to distribute it always if you're using any of the client features. The other three are optional for specific scenarios.

These DLLs should be distributed with your application as external files and must be accessible via the FoxPro Path in your application. The best place for these files typically is the same location as your EXE file.