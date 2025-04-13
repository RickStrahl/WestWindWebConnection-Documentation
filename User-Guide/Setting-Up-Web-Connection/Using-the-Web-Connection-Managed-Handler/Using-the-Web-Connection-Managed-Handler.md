Starting with Web Connection 5.25 Web Connection ships a .NET managed handler/module that completely duplicates - and enhances - the ISAPI DLL that has been Web Connection's mainstay. The module provides a much cleaner design for the connector interface and much easier development which in turn allows us to provide more advanced features in the module. The .NET module also runs more efficient than the ISAPI module in COM operation.

Here is a list of advantages of using the module:

* More efficient COM Pool Manager 
* Support for MTDLL servers for in-process operation (still not recommended)
* More flexible in the server interface - many small improvements
* Web interface auto-refreshes server load statistics
* Native IIS 7 Integrated Pipeline Support
* Pure xcopy deployment for scriptmaps through web.config
* Support for running on 64 bit Web Servers (for the module - VFP servers still are 32 bit!)
* Possibility to run on non-Microsoft platforms with Mono
* Do development without IIS installed using the [Web Connection Cassini or Visual Studio Web Servers](vfps://Topic/_2611D4RBH)
* Improved File Based Server Management (load/unload/load one/AutoStart/AutoReload of instances)
* More discoverable configuration
* Richer start/stop administration for File based operation

There's also one downside to using the module which relates to the way that ASP.NET and IIS 7 separate IIS applications:

* On pre-IIS 7 .NET 2.0 must be installed and ASP.NET configured
* .NET Handlers are tied to exactly one IIS application/virtual directory - 
no cross virtual COM sharing

### Load, Unload, AutoStart and AutoReload Support for File based Operation in the .NET Module
Unlike the ISAPI extension the managed module supports easy loading and unloading of servers in file based mode. To use this feature you need to configure the following keys:

```xml
<webConnectionConfiguration>
   <!-- NOTE: These settings apply only to the Web Connection Managed Module! -->
   <add key="ExeFile" value="c:\wwapps\wc3\wcdemo.exe"/>
   <add key="UpdateFile" value="c:\temp\uploads\wcdemo.exe"/>
   <add key="ServerCount" value="2"/>
   <add key="AutoStartServers" value="True"/>
Module&lt;/small&gt;"/>
</webConnectionConfiguration>
````

#### Manually starting servers via the Module Admin Page
The ExeFile determines the EXE that is launched when either manually starting a server with:

**StartExe.wc**  
**StartOneExe.wc**

The servers are launched in the context of the Application Pool that is hosting it - so typically SYSTEM or NETWORK SERVICE unless otherwise specified in the IIS ApplicationPool configuration. Make sure that an account is used that has all the permissions needed to access to all the server, network and database resources that you need access to.

#### Autostarting Servers via Config Settings
By setting the AutoStartServers to true and specifying a ServerCount and ExeFile the managed module will automatically start as many instances as of your EXE as you specify. 

#### AutoRestart of hung Servers
The Managed Module also includes a limited AutoRestart feature that checks for the number of active instances of the given EXE anytime a request times out. When a request times out the module looks for active instances of the EXEs still running in the Windows Task list and starts up as many instances as necessary to get to the ServerCount value.

Note that this feature is not 100% reliable in ensuring that all instances are always running. If you have more than one instance running and one dies, unless there's a timeout due to some other reason the module may not detect the missing server. Only when a request actually times out is the check operation incurred. Also, if a server times out and is hung without actually dying, the module doesn't know that the instance is hung - it sees the EXE as active even though it might be hung. If you want better restart functionality then using COM mode will provide more controls and guarantees that the specified number of servers are always active.

### .NET Modules are tied to a single Web IIS Application/Virtual
Unlike the ISAPI handler which runs globally in the IIS host process, the .NET Module is tied to a specific virtual directory/application in IIS and it's ASP.NET AppDomain. This means that a single module instance can't span multiple applications in the same way as the ISAPI connector can. This is due to ASP.NET's application boundary which is tied to a virtual directory/IIS Application and generally a good thing.

Typically this is not a problem as logic should be isolated and separate applications should be created as separate servers anyway, but it's important to understand the differences when building complex applications that potentially span multiple virtual directories.

Note that this applies only to COM mode operation - file mode with its completely disconnected instances of servers can still run across multiple virtuals.