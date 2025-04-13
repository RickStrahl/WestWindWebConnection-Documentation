Web Connection works under Windows 10, 8, 7 and Vista as well as under Windows Server 2016, 2012 and 2008. When installing Web Connection automatically configures itself for operation under these Windows versions. These versions use **IIS7**,**IIS8** and **IIS10** all of which use the same configuration interface and features. If you see a reference to IIS 7 assume it'll work in versions 7 through 10 as well.

This topic describes configuration through the Web Connection Console UI, using programmatic tools and manually configuring IIS and Web Connection by hand.

 
* **[Windows Components required for IIS](#WindowsComponentsrequiredforIIS)**
* **[Automatic Configuration](#AutomaticConfiguration)**
* **[Programmatic Tools](#ProgrammaticTools)**
* **[Manual Configuration](#ManualConfiguration)**

> #### @icon-warning All Configuration Tasks require Administrative Rights
> All IIS configuration tasks **have to** run as an Administrator. The latest versions of `Setup.exe` and `Console.exe` automatically prompt for rights elevation.

### Windows Components Required for IIS
In order to run Web Connection on Windows, IIS needs to be installed and correctly configured. IIS is **not installed by default** so you have to explicitly install it.

> #### <i class="fa fa-exclamation-triangle"></i> IIS has to be properly configured before Installation
> It's crucially important that the following IIS components are installed before you start installing Web Connection, otherwise you will see all sorts of installation errors. Make sure you configure IIS prior to Web Connection installation using the instructions below.

### Powershell IIS Feature Installation
We've provided a Powershell script that enables the IIS Web Services Windows Feature and installs all the Web server features required to run Web Connection. This works for both desktop and server versions of Windows.

Open an **Administrator Powershell Command Prompt**:
```powershell
PS> cd \wconnect
PS> Set-ExecutionPolicy Bypass -Scope Process
PS> .\Install-IIS-Features.ps1
```

For more info please on what this script does <a href="https://weblog.west-wind.com/posts/2017/may/25/automating-iis-feature-installation-with-powershell" target="top">check out this blog post</a>.

### Manually Installing IIS Features
On **Windows Clients** the IIS configuration features can be found under the **Turn Windows Features On and Off** from the Start Menu (Programs and Features). On **Windows Server** open Server Manager and **Add the Web Server Role** and follow the Wizard interface to add components. If IIS is already installed right click on the **Web  Server Role** and click on either **Add/Remove Role Services option** (more info on <a href="http://tinyurl.com/3y5xqyo" target="top">Server Roles</a>). The figures in this topic show the UI for the Windows Clients.

Once you are there the required components for Web Connection are as follows:

![](IMAGES/misc/IISFeatures.png)

The figure above highlights the critical components that are used by Web Connection and are absolutely required. 

The most important settings for Web Connection are:

* IIS Metabase and IIS 6 Configuration compatibility
* ASP.NET and .NET Extensibility
* ISAPI Extensions Support if you use the ISAPI module (classic mode)
* Basic and Windows Authentication

Note that the .NET version does not matter for the .NET components. Any version will do but if you are running Vista make sure to install .NET 4.0 as that's required for the .NET Module.

I also recommend you go through the rest of the options and configure features you need in general, such as Content Compression and logging features. When done click OK to install the configured setup. Now you're ready to install Web Connection.


### Automatic Configuration
Once the base components have been installed you can use Web Connection's automatic configuration to install Web Connection. You can use `Setup.exe` to install Web Connection or you can use `Console.exe` and use the *New Project Wizard* to create new projects or the *Server Configuration Wizard* to configure an existing Web site. 

In all cases you should use the IIS  server setting from the server type drop down or list.

![](IMAGES/ManagementConsole/setup1.png)

These tools automatically create virtual directories, application pools, script maps, set permissions and get your application ready to work. Generally this is the easiest way to start Web Connection applications on a development set up.

### Programmatic Tools
Web Connection includes a number of utility functions that let you programmatically create installation scripts. New projects by defualt create a configuration script as `yourProject_ServerConfig.prg` which you can customize. But the low level functions to handle all of these configuration task can also be directly accessed via code that you can write an integrate into your own programs and tools.

#### yourProject_ServerConfig.prg
When you create a new Web Connection Web Connection creates `yourProject_ServerConfig.prg` file that contains a script that can automatically configure a Web site for your application using the standard Web Connection Project format. This code assumes that the configuration is run out of the `Deploy` folder of a project and that the Web site lives in a relative path of `..\web\` from this location.

The install script creates or configures a virtual/root directory, creates an IIS Application Pool, creates a temp folder, sets permissions on the Web and temp folders, and registers your EXE server as a COM object (if it exists).

The script is also compiled into your EXE using a `CONFIG` command line switch, so you can run it with:

```
yourProject.exe CONFIG
```

You can control some configuration settings via the `[ServerConfig]` section in the `yourApp.ini`:

```ini
[ServerConfig]
Virtual=wconnect
ScriptMaps=wc,wcs,wcsx,wwd,blog
IISPath=IIS://localhost/w3svc/1/root
```

You can specify the `Virtual` directory to create in the relative `..\web\` folder. Specify any scriptmaps extensions you want to create for your application (or if you're using the .NET module you can leave this blank as they will already be configured in `web.config`.

Finally you can specify the Web site where these settings are applied. The default is: `IISPath=IIS://localhost/w3svc/1/root` which is the Default Web Site which has an ID of `1` (second to last path segment). You can find the ID of a Web site in the IIS Management Console by looking at *Advanced Settings* and the ID Property in that view. 

#### Configuration Scripting Tools
The serverConfig file uses Web Connection's built in scripting functions that let you create virtuals and scriptmaps, as well as set path permissions. If you want to fully automate the process in a custom way use `yourProject_serverconfig.prg` as a template to start from.

In addition you can also access these tools via `Console.exe` which provides access to `VIRTUAL`, `SCRIPTMAP`, `DCOMCONFIG` via [Command Line Options to the Console](VFPS://Topic/_0R10SLEAN). 

For example:

**Create Virtuals:**
```foxpro
***Using the Console - last parameter is the IIS Admin path under which virtual is created
* **.NET Handler
DO Console WITH "Virtual", "WebDemo","c:\westwind\webdemo",.F.,"IIS7HANDLER","IIS://localhost/W3SVC/1/ROOT"
* **ISAPI
DO Console WITH "Virtual", "WebDemo","c:\westwind\webdemo",.F.,"IIS7","IIS://localhost/W3SVC/1/ROOT"

* **Interactive
DO Console WITH "Virtual","UI"
```

**Create Scriptmaps:**
```foxpro
* **Using the Console
DO Console WITH "ScriptMap", ".wxx","c:\westwind\webdemo\bin\wc.dll",.F.,"IIS7","IIS://localhost/W3SVC/1/ROOT/WebDemo"

* **Interactive
DO Console WITH "ScriptMap", "UI"
```

Note scriptmap creation will also register the ISAPI extension with the ISAPI restriction list.

## Manual Configuration
If you think you don't want to use any tools to configure your application for you, or you really want to understand what settings are required to run your application, the following topics take you through manual configuration for each of the configuration settings.

### Create an Application Pool
Any virtual directory or root site in IIS is hosted in what is known as an Application Pool. An Application Pool is the host processes for one or more Web applications. You can see these host processes running as `w3wp.exe` in Task Manager. There can be multiple Application Pools running at the same time each mapping one or more Web sites/virtuals. Every application must be assigned to an Application Pool, but multiple applications can share a single Application Pool.

To create an Application Pool in IIS:

* Go to the Application Pools section in the Connections Tree
* Right click and select *Add Application Pool*
* Create with the name of your choice (West Wind Web Connection)
* Choose .NET Framework **4.0** and **Integrated** Managed Pipeline Mode

![](IMAGES/misc/CreateApplicationPool.png)

Once you've created the Application Pool select it in the list and click on Advanced Settings. 

#### Set the Account Identity 
In the Application Pool settings set the Identity for the Application Pool to `LocalSystem` or whatever account you require to get the permissions you need to run the application. For the latter chose `SpecificUser` and then enter the username and credentials. 
 
By default the user identity you choose here also is passed to your your Web Connection FoxPro server, so make sure you use an account that has the rights your application needs.

![](IMAGES/misc/ApplicationPoolAdvanced.png)
 
You can also configure various other settings such as the process recycling, idle timeout and various other flags. 
> #### @icon-exclamation-circle Enabling 32 bit Applications on 64 bit Machines for ISAPI
> If you are using the `wc.dll` ISAPI module on a 64 Bit machine you **have to** so set the **Enable 32 Bit Applications** flag to `True` so that the 32 bit wc.dll can work. The .NET Module can run natively in 64 bit mode but we still recommend you run in 32 bit mode.

### Virtual Directory Configuration
In order to run a Web Connection application you will need to create a virtual directory. The virtual can be configured in the IIS Servies Manager. To create a new virtual directory:

* In the Connections tree go to YourMachine | WebSites | YourWebSite and click *Add Application*
* Enter the name of the virtual directory (WebDemo)  and the path to the Web directory
* Select the West Wind Web Connection Application Pool if it exists

<img src="IMAGES/misc/IIS7Virtual.png">

Once the virtual has been created select the Authentication option in the virtual's configuration.

Enable:

* Anonymous Authentication
* Basic Authentication
* Windows Authentication

<img src="IMAGES/misc/IIS7VirtualAuthentication.png">

### Setting the ISAPI Restrictions for each copy of wc.dll
*applies only to the ISAPI module, not the .NET module*

Due to the security settings in IIS generic ISAPI and CGI extensions (and ASP and ASP.NET even) are not allowed to execute. Without this setting any direct access or access through scriptmaps (MyPage.wcsx) to the dll will generate a 404.1 (File not found) error. 

The extension needs to be explicitly enabled:

* Open IIS Management Console
* Go to the Machine Root in the Connections tree
* Select ISAPI and CGI Restrictions (IIS Settings)
* Click and add your mapping to wc.dll in your Web path

<img src="IMAGES/misc/IIS7IsapiRestrictions.png">

> #### @icon-warning Changed behavior: bin/wc.dll execution is not allowed
> IIS 7 and later does not allow direct URL access to any code in a BIN directory. For Web Connection this means that URLs that point at something like this:
>
> http://www.west-wind.com/wconnect/bin/wc.dll?wwDemo~TestPage
>
> are not allowed. There's an easy workaround however.
>
> **Use Scriptmaps**  
> There's no need to EVER call wc.dll directly. Instead you should always a scriptmap that points to wc.dll instead. Web Connection always installs the .wc extension so any URL to wc.dll can be easily rewritten using wc.wc?wwDemo~TestPage to provide the same behavior. It's a simple search and replace to switch to scriptmaps. It's more secure and more flexible as you don't have to reference the dll in its exact path.

### Creating Script Mappings
You'll also want to configure various script maps for your Web Connection application and you do this by creating mappings between certain extensions (like .wc, .wcsx, or .yourmap) to the ISAPI wc.dll or the managed .NET Handler webconnection.dll. This allows you to serve pages like MyRequest.wc which automatically can route to a MyRequest process method in Web Connection.

The preferred mechanism in IIS7 and IIS8 is to use the .NET Managed Handler, but you can also use the ISAPI extensions for classic applications or applications that span multiple virtual directories.

The process for the .NET Handler and ISAPI extension are slightly different.

**.NET Managed Handler**
* Go to the Connections Tree and find your Virtual Directory created earlier
* Select Handler Mappings
* Choose *Add Managed Handler* on the right sidebar 
* Enter your extension as *.wp where wp is the extension
* Enter the path to the WebConnectionModile.dll
* Enter a descriptive name
* Click on Request Restrictions
* Uncheck *Invoke handler only if file exists*
* Select Allow All Verbs (or GET/POST at minimum) on the Verbs page
* Select Script on the Access page

<img src="IMAGES/misc/IIs7ScriptMap1_Managed.png">

**ISAPI ScriptMap**
* Go to the Connections Tree and find your Virtual Directory created earlier
* Select Handler Mappings
* Choose *Add Script Map* on the right sidebar 
* Enter your extension as *.wp where wp is the extension
* Enter the path to the wc.dll in your virtual's \bin folder
* Click on Request Restrictions
* Uncheck *Invoke handler only if file exists*
* Select Allow All Verbs (or GET/POST at minimum) on the Verbs page
* Select Script on the Access page

<img src="IMAGES/misc/IIs7ScriptMap1.png">

On both types set the access restrictions:

<img src="IMAGES/misc/IIs7ScriptMap2.png">

### Web.Config Configuration
Note that all Virtual directory specific configuration settings are written to the web.config file in the Web application's Web root path by IIS, and you can also make setting changes in this configuration file. 

**.NET Managed Handler**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
   <system.webServer>
      <validation validateIntegratedModeConfiguration="false"/>
      <handlers>
         <add name=".wc_wconnect-module" path="*.wc" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" preCondition="integratedMode"/>
         <add name=".wcsx_wconnect-module" path="*.wcsx" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" preCondition="integratedMode"/>
         <add name="wp_wconnect" path="*.wp" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" preCondition="integratedMode"/>
      </handlers>
   </system.webServer>
</configuration>
```

Note that the managed handler configuration is 100% portable - if you copy the web.config file these script mappings are automatically available in the copied location.

**ISAPI**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
... omitted
    <system.webServer>
        <handlers>
            <add name="WebDemo-wcsx" path="*.wcsx" verb="GET,POST" modules="IsapiModule" scriptProcessor="c:\westwind\webdemo\bin\wc.dll" resourceType="Unspecified" requireAccess="Script" responseBufferLimit="0" />
            <add name="WebDemo-wp" path="*.wp" verb="GET,POST" modules="IsapiModule" scriptProcessor="c:\westwind\webdemo\bin\wc.dll" resourceType="Unspecified" requireAccess="Script" responseBufferLimit="0" />
            <add name="WebDemo-wc" path="*.wc" verb="GET,POST" modules="IsapiModule" scriptProcessor="c:\westwind\webdemo\bin\wc.dll" resourceType="Unspecified" requireAccess="Script" responseBufferLimit="0" />
            <add name="WebDemo-wwsoap" path="*.wwsoap" verb="GET,POST" modules="IsapiModule" scriptProcessor="c:\westwind\webdemo\bin\wc.dll" resourceType="Unspecified" requireAccess="Script" responseBufferLimit="0" />
        </handlers>
    </system.webServer>
</configuration>
```

Note that the ISAPI extension mappsing **are not**100% portable as they include hardcoded paths. You can easily move the web.config file and adjust paths as necessary however which can often be easier than using the IIS management console to add script maps.

### Security
The following are two very important security settings that you need to make on your Web site in IIS and in the file system. If you use the `yourApp_ConfigureServer.prg` for your application or the older (pre-6.10) **Server Configuration Wizard** these settings are automatically made for you.

For more detailed info on securing your site please see:

* [Securing your Web Connection Application](https://west-wind.com/webconnection/docs/_00716r7og.htm)

For manual configuration you have to explicitly set these settings up.

### Important: Lock down the `Admin` Folder
When you install your site on a live server it's **vitally** important that you secure the `Admin` folder so that you have to authenticate to access the Administration page. 

Start by enabling security on your Web site or Virtual:

![](IMAGES/misc/enableauthentication.png)

Next lock down the **Admin** folder. Remove or set **Deny** permissions for any unauthenticated users in the Admin folder and specifically the `IUSR` account.
 
![](IMAGES/misc/windowsauthenticationadminlockdown.png)

> #### @icon-info-circle Remote Access to the Adminstration Page only works Authenticated
> Starting with Web Connection 6.19 the admin page no longer displays for unauthenticated users when accessing the site remotely. Instead an error message is displayed. Local access - useful for development - continues to work without authenitication but also displays the error message, along with the actual admin links
>
> ![](IMAGES/misc/adminpageaccessdenied.png)


### Temp File Path Permissions
If you are running Web Connection in file based mode for development (or in production) make sure that the temp folder configured in the *Configuration/WebConnectionConfiguration/TempPath* setting in web.config (.NET Handler) or the *Path* setting in wc.ini (ISAPI) exists and that it **has full read and write access for the Application Pool's user account**.

In recent versions this path is a relative path that points `.\temp` from your project's `\deploy\temp` folder. In order versions this path may be in a different location - check in `wc.ini`, or `web.config`. This is vitally important in order for IIS and the Application Pool to be able to read and write data in that path. It's also vitally important that your Web Connection Server - potentially running under COM impersonation - has full rights in this folder which is determined by the **Application Pool Identity** described earlier.

**User Account Control**
Note that UAC can affect access to local folders. We highly recommend that if you use custom accounts for either the Application Pool or COM Impersonation, that you EXPLICITLY add the account to the permissions to avoid problems with the Administrators group under UAC (which effectively leaves you not an administrator).