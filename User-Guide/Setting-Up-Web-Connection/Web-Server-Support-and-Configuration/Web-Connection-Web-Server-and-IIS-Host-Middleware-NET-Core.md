The Web Connection Web Server is a self-contained, non-admin, local Web Server that lets you to run Web Connection application without a dependency on an explicit external Web Server like IIS. 

The server is specifically built for Web Connection so it includes everything it needs to run Web Connection applications from **a standalone EXE that you can distribute with your application** and that you run run locally **from any machine**. 

It's ideally suited to run for local Web Connection development while creating your applications.

> #### @icon-info-circle Requires .NET Core Runtime Installation
> The Web Connection Web Server is .NET Core based and requires a .NET Core Runtime to run. You have to ensure an appropriate .NET Core Runtime or SDK is installed *(v5 < 7.26, v6 > 7.26, v8 >= 7.40)*. To see which version is or needs to be installed you can run the `\WebConnectionWebServer\install-dotnet.ps1` PowerShell script. [more info below](#webconnection-web-server-installation-and-configuration)

Using this server also allows you to create 'no-install' Web sites that can run on a local machine or local network servers **without any additional Web Servers** other than a .NET Core Runtime. Assuming your site is set up and configured you can start the server `.exe` and your site is up and running - from any machine.

![](IMAGES/misc/WebConnectionWebServer.png)

For traditional server hosting the Web Connection Web Server **can also run inside of IIS using the ASP.NET Core Hosting Module**, where it runs as a high performance, in-process server module. This allows you to use the standalone server for development, and IIS hosted for deployed applications running the exact same server in development and production.

## Quick Start
The easiest way to use the Web Connection Web Server during development is to use the `Launch()` command in your project folder:

```foxpro
Launch("WEBCONNECTIONWEBSERVER")
```

or just:

```foxpro
Launch()
```

If you used the Web Connection Web Server when you created the project.

`Launch()` starts the Web Connection Web Server console application (if it's not already running), starts the FoxPro server and launches the browser at http://localhost:5200. You can use alternate ports or a secure link by modifying the `Launch.prg` script in your project folder.

## Manual Launching as a Console Application
The server runs as a Console executable from the Windows command line and can be fully automated via a handful of command line options. The server by default is configured to run the Web Connection application application it is installed with, so typically you just need to run the `.exe` as is without. 

There's `WebConnectionWebServer.json` that holds the default configuration which can be customized as needed.

> ##### @icon-warning Console Application
> `WebConnectionWebServer.exe` is a **Console Application** and so the Terminal window stays up and running until you either press `Ctrl-C` in the terminal or close the terminal window.

The main goal for this server is to provide a local development experience that doesn't require IIS or IIS Express. You can install it without other dependencies except for a dependency on the [.NET Core Runtime or SDK](https://dotnet.microsoft.com/download). 

> #### @icon-info-circle Self-Contained Local and Intranet Web Sites
> A self-contained Web Server may not sound that impressive, but it is actually quite useful: You can easily deploy a **local, fully self contained application** that runs a Web Connection Web site. Since **the Web Server is part of the project** everything you need to run the site can be bundled into an XCOPY deployable folder. 
>
> To launch your site you can then simply do:
>
> ```ps
> cd \WebConnectionProjects\TestProject
> # Launch the Web Connection FoxPro EXE server (not necessary if using COM)
> .\deploy\testproject.exe
> # Launch the Web Server
> .\WebConnectionWebServer\WebConnectionWebServer.exe
>```
> 
> You can run a site on a local machine either in a browser, or build a desktop application around it that accesses the site via a Web Browser control, or makes REST API calls into the Web Connection application. If you expose the port used by the server to the Intranet or Internet the application can also be externally accessed.

## WebConnection Web Server Installation and Configuration
The server is a .NET Core application so in order to work you'll need:

* .NET Core Runtime (v8 or v6 or v5 or v6 depending on WWWC version)
* A configuration file `WebConnectionWebServerSettings.xml` in the Web Root
* For IIS Hosting: A custom `web.config` configuration

Different Web Connection versions use different versions of the .NET Runtime for the Web Connection Web Server:

* **Web Connection v7.36 and greater**   
uses [.NET 8.0](https://dotnet.microsoft.com/en-us/download/dotnet/8.0)

* **Web Connection v7.26 - v7.35**   
uses [.NET 6.0](https://dotnet.microsoft.com/en-us/download/dotnet/6.0)

* **Web Connection v7.0 - v7.25**   
uses [.NET 5.0](https://dotnet.microsoft.com/en-us/download/dotnet/5.0)

To install the appropriate runtimes, download and install either one of these:

* **64 bit Hosting Bundle** 
* **64 bit SDK**

### .NET Core Runtime Requirement: What to install
In order to run this Web Server **you need to install .NET and ASP.NET Hosting Runtimes** which are contained either in the **.NET SDK** (Runtimes + various .NET tools and compilers) or the **.NET Core Hosting Bundle** (just the runtimes). To check if you need to install the runtimes on your machine you can run `install-dotnet.ps1` in the `WebConnectionWebServer` folder from a Powershell command prompt. If the runtimes are not installed it will prompt you to download the SDK or Hosting Bundle.

## Running the WebConnectionWebServer
The Web Connection server is implemented as a **.NET Core  Console application** that runs from the Windows command prompt or Powershell. Alternately you can also it from Explorer by double clicking the `WebConnectionWebServer.exe`. 

> ##### @icon-warning Console Application
> `WebConnectionWebServer.exe` is a **Console Application** and so the Terminal window stays up and running until you either press `Ctrl-C` in the terminal or close the terminal window.

There are several ways to launch the server:

* From the `Deploy` folder in the VFP IDE use `Launch("WebConnectionWebServer")`
* Using `Console.exe WebConnectionWebServer` from Command Prompt in a Project folder
* Running `WebConnectionWebServer.exe` and specify a `--WebRoot`
* Running `WebConnectionWebServer.exe` out of a Web Root folder (ie. `MyProject\web`)

> #### @icon-warning Server Ports
> By default the server launches on **port 5200**. You can override the port using the `--port` switch. The server requires a dedicated port - two instances of the server or another server application can't share the same port so if you run into port already in use error you may have to override with the `--port` switch.

### Using Launch()
The Web Connection `Launch()` command can be used from your project to launch the Web Connection Web server and your FoxPro application server:

```foxpro
Launch("WebConnectionWebServer")
```

If you're running launch in a project folder, the server is automatically opened in the `..\web` folder as its Web root. Requests are served by default on **port 5200**  (ie. `http://localhost:5200`). You can also use `https://` via the `--useSSL` switch.

### Explicitly specify a Path, Port and other Options
You can explicitly launch the Web Server and specify a path that becomes the Web root folder by opening a command prompt and then use `WebConnectionWebServer`

```ps
# 'global' server (from `install-dotnet.ps1`) - specify the Web Root
WebConnectionWebServer --WebRoot c:\webconnectionProjects\MyProject\web

# explicit Path and provide additional launch options
..\WebConnectionWebServer\WebConnectionWebServer `
                       --WebRoot c:\webconnectionProjects\MyProject\web `
                       --port 5201 `
                       --UseSsl

# global instance - launch in Web Root Folder
WebConnectionWebServer
```

To see all the options type:

```ps
WebConnectionWebServer --help
```

### Launch in the project's Web Root folder
Alternately you can launch the application in the folder you want to host as a Web site:

```ps
cd c:\webconnectionProjects\MyProject\web
WebConnectionWebServer
```
### Shut down the Web Connection Web Server
The application is a **Console Application** that runs in PowerShell or Windows Command Prompt Terminal and displays status information as it runs by default (you can disable this via options) in the Terminal. The server runs in the Terminal until you explicitly shut it down via `Ctrl-C` or `Ctrl-Break`, or you close the Terminal window.

### Server Configuration Settings
The `WebConnectionWebServer` uses a new and separate configuration file `WebConnectionWebServerSettings.xml` that control operation of the Web Server application in both standalone and IIS hosted modes. This file contains both server settings and Web Connection settings **in one place**. 

If you run in IIS, some IIS specific settings (like request size, filters etc.) may still have to be set in `web.config` but any Web Connection related settings are set in the server config file.

#### WebConnectionServerSettings.xml
The Web Connection specific features are configured in  `WebConnectionServerSettings.xml`. Note that this is different from the old .NET Module which used settings in `web.config`. The new settings map very closely to the settings that the classic .NET module but the format uses simpler, raw XML elements style rather than config style settings in web.config.

```xml
<?xml version="1.0" encoding="utf-8"?>
<WebConnectionWebServerSettings xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
	<!-- Server Configuration-->
   <HandledExtensions>.wc,.wcs,.wwd,.blog,.md</HandledExtensions>
   <DefaultFiles>index.html,default.htm,default.html,default.blog</DefaultFiles>
   <VirtualDirectory>/</VirtualDirectory>
   <Timeout>90</Timeout>
   
   <!-- Message Mode and Server Startup -->
   <MessagingMechanism>File</MessagingMechanism>
   <ServerCount>2</ServerCount>
   <AutoStartServers>false</AutoStartServers>
   
   <!-- Com Based -->
   <ComServerProgId>wcDemo.wcDemoServer</ComServerProgId>
   <ComServerLoadingMode>RoundRobin</ComServerLoadingMode>
   <UseStaComServers>false</UseStaComServers>
   
   <!-- File Based -->
   <TempPath>~\..\temp</TempPath>
   <TempFilePrefix>WC_</TempFilePrefix>
   
   <!-- Administration -->
   <AdminAccount>ANY</AdminAccount>
   <AdminPage>~/admin/Administration.wc</AdminPage>
   <LogDetail>false</LogDetail>
   <MessageDisplayFooter>Page generated by Web Connection IIS Connector Module</MessageDisplayFooter>
   
   <!-- Server Updates -->
   <UpdateFile>~/../wcdemo_update.exe</UpdateFile>
   <ExeFile>~/../wcdemo.exe</ExeFile>
   
   <!-- Live Reload --> 
   <UseLiveReload>true</UseLiveReload>
   <LiveReloadExtensions>.wc,.wcs,.wwd,.html,.htm,.css,.js,ts,.usm,.blog,.md</LiveReloadExtensions>
   
   <!-- Web Sockets -->
   <UseWebSockets>true</UseWebSockets>
   <WebSocketProcessScriptmap>wwd</WebSocketProcessScriptmap>
</WebConnectionWebServerSettings>
```

> ##### @icon-info-circle First Time Startup copies Settings from web.config  
> If the `WebConnectionWebServerSettings.xml` file doesn't exist and there is an existing ASP.NET  `web.config` file that holds Web Connection settings, the `WebConnectionWebServerSettings.xml` file is automatically created from the settings in `web.config`. The Middleware Admin page also includes a link that lets you import settings from a `web.config` file explicitly, but you should only do this once.


### Command Line Options
The `WebConnectionWebServer` application has a number of command line options that allow you to customize how the server runs. You can look up the options are available via the `--help` flag.

Here are the options available:

```text
Syntax:
-------
WebConnectionWebServer  <options>

Commandline options (optional):

--WebRoot            <path>  (current Path if not provided)
--Port               5200*
--UseSsl             True|False*
--UseLiveReload      True*|False
--ShowUrls           True|False*
--OpenBrowser        True*|False
--DefaultFiles       "index.html,default.htm,default.html"

Live Reload options:

--LiveReload.ClientFileExtensions   ".cshtml,.css,.js,.htm,.html,.ts"
--LiveReload ServerRefreshTimeout   3000,
--LiveReload.WebSocketUrl           "/__livereload"

Configuration options can be specified in:

* Environment Variables with a `WEBCONNECTION_` prefix. Example: 'WEBCONNECTION_WEBROOT'
* Command Line options as shown above

Examples:
---------
WebConnectionWebServer --WebRoot "c:\temp\My Site" --port 5500 --useSsl false

$env:WEBCONNECTION_PORT 5500
WebConnectionWebServer
```

## Hosting in IIS
WebConnectionServer is a self-contained Web Server that can run as an executable, but it can also be hosted inside of IIS providing the exact same functionality as the standalone using .NET Core hosting. Instead of using the built-in native .NET Kestrel Web Server and running the standalone EXE, the application uses IIS as the Web Host with an IIS module to interface directly with the .NET Core binary (`WebConnectionWebServer.dll`).

### Do you need IIS Hosting? 
For development IIS is not required nor recommended. IIS configuration requires Admin privileges and configuration can be daunting (even when automated. Instead the standalone development server can run as a regular user and is entirely xcopy deployable within a project. Move the project and there's nothing else that has to be configured to run the application.

For production sites you definitely want to use IIS as it provides better performance and many of the advanced features of IIS like server lifetime management, static file caching, multi-domains for a single IP address, SSL management and much more.

When using IIS, WebConnectionWebServer runs as an In-Process (or optionally out of Process) ASP.NET Core Module which then hosts the .NET Core Web Connection Web Server. This process is very efficient and similar to how classic ASP.NET handlers and modules run. Performance compared to classic ASP.NET is roughly on par or perhaps very slightly better with less memory usage. On the downside, .NET Core modules can not be hot-swapped while running as the executable is locked while loaded by IIS requiring a brief shutdown to replace the Web Connection Web Server module.

### IIS Requirements and basic Configuration
In order for this to work you need the following on your production server:

* **.NET Core Runtime**
* **ASP.NET Core Runtime**  

To install these:

* Install either the [ASP.NET Core Hosting Bundle](https://dotnet.microsoft.com/download) or the [.NET Core SDK](https://dotnet.microsoft.com/download) <small>(see above)</small>
* Copy the `.\WebConnectionWebServer` folder into your Project's Root Path (should be there in a new project)
* Create your Web Site (don't recommend using Virtuals)
* Point the Web Site folder at the `.\WebConnectionWebServer` folder in your project
* Set up a new Application Pool and set **No .NET Managed Code**
* Set up Application Pool Identity to SYSTEM to start - then dial back when it all works
* Make sure you have `WebConnectionWebServerSettings.xml` into your `.\web` folder
* Check settings in `WebConnectionWebServerSettings.xml`
* If `WebConnectionWebServerSettings.xml` doesn't exist it'll be created on the first request

The `web.config` in the `.\WebConnectionWebServer` folder at minimum should look like this:

```xml
<configuration>
    <system.webServer>
        <!-- <handlers> ...comment old ASP.NET handler maps... </handlers> -->
        
        <handlers>
          <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
        </handlers>
        <aspNetCore processPath="dotnet.exe" 
            arguments="..\WebConnectionWebServer\WebConnectionWebServer.dll" 
            stdoutLogEnabled="false" 
            stdoutLogFile=".\logs\stdout"
            hostingModel="InProcess">
            <environmentVariables>
                <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Production" />
                <environmentVariable name="WEBCONNECTION_OPENBROWSER" value="False" />
                <environmentVariable name="WEBCONNECTION_SHOWURLS" value="False" />
                <environmentVariable name="WEBCONNECTION_USELIVERELOAD" value="False" />
              </environmentVariables>
        </aspNetCore>
    </system.webServer>
</configuration>
```

This setup:

* Enables the ASP.NET Core Hosting Module 
* Launches `dotnet.exe` with the Web Connection Server module as the starter   `..\WebConnectionWebServer\WebConnectionWebServer.dll`
* Uses `InProcess` hosting (you can also use `OutOfProcess`)
* Sets up the startup environment:
    * Turns off Live Reload, Open Browser and Show Urls
    * These are not useful in hosted mode
* Optionally enable the logs via `stdoutLogEnabled="true"` in the `/logs` folder.

Notice that the path to the `..\web\WebConnectionWebServer.dll` is a relative path to the `web.config` where the above is defined, meaning that this `web.config` configuration is portable. If you move the project to a new folder the server link will continue to work.

> If you get IIS startup errors like *HTTP Error 500.0 - ANCM In-Process Handler Load Failure*, try hosting using `OutOfProcess` which is more forgiving. Error messages for that particular error can be found in the Event Log. Any errors during startup are logged into the Application Event Log (see error message). One common failure point is make sure Windows Authentication is enabled on the Web Site/Virtual


### Port Sharing for Port 80
The most pressing problem that IIS solves is that you can't share ports with multiple server instances easily with Kestrel, but IIS makes that easy via its host header bindings. If you're running only a single site then this might be doable, but for multiple Web sites on a Web Server you'll want to definitely go through IIS.

### Application Lifetime Management
IIS also provides lifetime management services to ensure that the .NET Core application is up and running and responding. If the application dies IIS quietly shuts it down and starts up a new instance, so IIS (actually the IIS Hosting Service) acts as a lifetime manager.

### Static File Services
IIS can also serve static file content as opposed to letting .NET Core perform that task. On Windows IIS and IIS's Kernel caching **are by far the fastest way** to serve static content that can be cached and compressed, and IIS makes this easy and transparent. The built-in Kestrel Web Server can serve all static resources internally too, and while it can do this capably, it's much less efficient compared to IIS.

To let IIS handle common static files, you can map `StaticFileModule` handlers explicitly to the StaticFileModule handler in IIS **prior** to the .NET Core module. Note that the Core module maps `path="*"` which is **every file** except the ones above explicitly mapped to something else. 

By adding specific handlers for specific extensions you can have IIS take over the most common file types:

```xml
<handlers>
      <!-- For IIS in Production: Let IIS Handle Static Files -->
      <add name="StaticFileModuleHtml" path="*.htm*" verb="*" modules="StaticFileModule" resourceType="File" requireAccess="Read" />
      <add name="StaticFileModuleText" path="*.txt" verb="*" modules="StaticFileModule" resourceType="File" requireAccess="Read" />
      <add name="StaticFileModuleSvg" path="*.svg" verb="*" modules="StaticFileModule" resourceType="File" requireAccess="Read" />
      <add name="StaticFileModuleJs" path="*.js" verb="*" modules="StaticFileModule" resourceType="File" requireAccess="Read" />
      <add name="StaticFileModuleCss" path="*.css" verb="*" modules="StaticFileModule" resourceType="File" requireAccess="Read" />
      <add name="StaticFileModuleJpeg" path="*.jpeg" verb="*" modules="StaticFileModule" resourceType="File" requireAccess="Read" />
      <add name="StaticFileModuleJpg" path="*.jpg" verb="*" modules="StaticFileModule" resourceType="File" requireAccess="Read" />
      <add name="StaticFileModulePng" path="*.png" verb="*" modules="StaticFileModule" resourceType="File" requireAccess="Read" />
      <add name="StaticFileModuleGif" path="*.gif" verb="*" modules="StaticFileModule" resourceType="File" requireAccess="Read" />
      <add name="StaticFileModuleWoff" path="*.woff*" verb="*" modules="StaticFileModule" resourceType="File" requireAccess="Read" />
      <add name="StaticFileModuleTxt" path="*.txt" verb="*" modules="StaticFileModule" resourceType="File" requireAccess="Read" />
      
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
    </handlers>
```

This lets IIS do what it does best, and the application server do what it does best (serve dynamic Web Connection content).

### IIS Startup Troubleshooting
When an error occurs during startup there's not a lot of information available. This may show in two ways:

* Site doesn't come up at all (connection refused errorrs)'
* You can ASP.NET Core Module (ANCM) Error Page

The former means the application couldn't launch at all and the module even failed to load. The latter managed to get hte module loaded, but it failed to properly launch the ASP.NET Core application. The latter page has additional debug information along with a link to a Microsoft Doc Page with more suggestions for debugging startup errors.

The following are helpful and address common failures:

#### Errors are stored in the Application Event Log
If the Web application fails to launch, IIS and the ASP.NET Core Module write errors into the **Application Event Log**. You can use the Event View to find errors for `IIS ASP.NET Core Module V2`.

#### Make sure the IIS Site has Windows Authentication Enabled
The most common startup error you may see is that your IIS Web site **has to have Windows Authentication enabled**. Windows Auth is used for securing the Admin pages and when running under IIS ASP.NET core use the IIS Windows Auth features to authenticate users.

To enable go into the IIS Service Manager for your site, find **Authentication** and enable **Windows Authentication**.

## Standalone Execution and Exposing the Web Server To the Network
When running the standalone Web Server as an EXE by default the Windows Firewall will block access to the server by IP Address plus port. In order to enable cross-machine or Internet access (if you enable DNS accordingly) requires that you open up the firewall for the inbound port your server is listening on - port 5200 by default.

To do this you can use the following Powershell script (requires Admin rights):

```ps
netsh advfirewall firewall add rule name="Web Connection Server (port 5200)" dir=in action=allow protocol=TCP localport=5200
```