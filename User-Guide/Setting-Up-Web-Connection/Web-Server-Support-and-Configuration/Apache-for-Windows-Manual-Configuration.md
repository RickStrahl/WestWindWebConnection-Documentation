Web Connection supports Apache 2.4 and 2.2 in ISAPI mode using a custom version of **mod_isapi**.

> #### @icon-info-circle Use Apache Only for Very Specific Use Cases
> Although Web Connection supports Apache via an ISAPI mapping module, the support provided is only minimal. Although it works, there's a performance penalty for the intermediary module usage, and you can only run in ISAPI mode which has been mostly replaced by the more efficient and more configurable .NET Mode in IIS. We also provide only minimal configuration support for Apache and won't support server configuration issues since we don't have the expertise to support Apache.
>
> Apache use should be limited to very specific use cases where you are already running Apache for other operations/apps and you need to interact with those applications in the same server environment. For all other cases we **highly recommend you use IIS** as it is the native Windows solution that is much more flexible for use with Web Connection.

### Web Connection Management Console Support
Web Connection provides **basic** configuration support for Apache for initial Setup that configures the sample application, as well as the new Project Wizard to create a new project.

The configuration set is minimal, and supports the following:


* Registers the Web Connection Apache Module
* Creates a virtual directory on the root site (no support for site creation)
* Sets any configured script maps

Setup only supports configuration of 'virtual' folders - there's no support for sites at the moment, but you can modify the configuration to easily add a `#virtualServer` after the fact. There's also no explicit support security configuration as Apache runs under whatever account it was started under.

The simple auto-created configurations are good enough to get up and running, but please ensure that you review your security requirements for server lockdown of administrations links etc.

## How it works
Apache support is mainly provided by a custom Apache module, plus a custom `wwApacheRequest` class that accounts for the differences in the HTTP data that Apache returns vs. what IIS returns. The main difference in the HTTP data is that Apache uses very different path formatting for various server settings and doesn't natively return any server paths like path to the active virtual.



### The Apache Module
The `mod_webconnection_isapi.iso` module is a custom implementation of Apache's `mod_isapi.iso`. The custom module's job is to smooth out some of the differences between the Apache ISAPI implementation and the actual ISAPI spec implemented in IIS. All the changes we've made relate to passing updated path information to Web Connection ISAPI handler.

This custom module provides the following over the standard `mod_isapi` module:

* Basic Authentication against Windows User accounts
* Path fix ups to match IIS pathing formats
* Several Marker Server Variables

### Custom wwApacheRequest Class
There's also custom `wwApacheRequest` class that handles the path differences between Apache and IIS to provide a mostly interchangeable environment for Apache. Most operations work the same for Apache as it does for IIS based servers. Exceptions are related to server pathing, which is fixed up dynamically by the module and depends on conventions.

> #### @icon-info-circle ISAPI DLL Location Requirement for Paths
> In order to ensure Web Connection can properly fix up paths, make sure that the `wc.dll` ISAPI dll is always located in the Site's/Virtual's `bin` folder. We also recommend you only use scriptmaps (extension mapping) for accessing all requests rather than direct DLL access.

### Web Connection Console Support
Web Connection also provides basic installation features for the following operations through the Setup and Console applications:

* Initial Web Connection Sample Setup (as part of Setup.exe)
* Create New Project from the Console

> #### <i class="fa fa-warning" style="color: firebrick"></i> Apache Security and Configuration is your Responsibility 
> Please note that if you plan on using Apache, you should be familiar with Apache configuration and security. It is your responsibility to lock down Apache server and your application and provide common customization features like virtual hosts etc. 
>
> Web Connection only provides **basic** Setup and New Project Wizard functionality to get up and running. Beyond that you're on your own as we don't know much about Apache.

> #### <i class="fa fa-warning" style="color: firebrick"></i> Apache is Case Sensitive!
> When creating sites and virtual directories please be aware that Apache is case sensitive in paths referenced over the Web. We recommend you **use lower case** for your virtual directory during installation.

### Manual Configuration
Manual configuration of Apache is accomplished by:

* Copying the Web Connection Apache ISAPI module
* Copy the Web Connection DLL
* Modify the httpd.conf file by
    * Create a virtual `Alias` Directory
    * Create Script maps via `ScriptAlias` 
    * Configure permissions and dll mapping
* Set CoInitialize=1 in `wc.ini` for COM operation
* Set the `wwApacheRequest` class in wwServer for request handling
* Access the Module Configuration Page via `ModuleAdministration.wc`

#### Copying the Web Connection Apache Module
The Web Connection module is found in the /scripts directory of the Web Connection installation. You should always copy this directory with your application to the server so the scripts directory is available to copy the files from within it to the server. To copy the file:

```dos
COPY FILE "<webconectionInstallFolder>\scripts\mod_webconnection_isapi.so" TO;
          "<ApacheRoot>\modules\mod_webconnection_isapi.so"
```

If you're using a version of Apache **prior to 2.4** use:

```dos
COPY FILE "<webconectionInstallFolder>\scripts\mod_webconnection_isapi_22.so" TO;
          "<ApacheRoot>\modules\mod_webconnection_isapi.so"
```

We highly recommend you use Apache 2.4 or later.

>#### Visual C++ Runtimes Required for 2.2 and 2.0 versions
>Apache doesn't ship with recent Microsoft CRT runtimes for Apache 2.2 and 2.0, so you have to copy  the Visual Studio 2005 runtimes for the 2.2 and 2.0 versions into your Windows directory if it's not already there (servers often won't have it). Apache 2.4 is compiled with Visual Studio 2015 so the runtimes are included in the install and no explicit download is required. [Runtime Downloads can found here](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads).

#### Create an Apache virtual directory
A virtual directory (or Site) is typically created under the `Apache/htdocs` folder, but you can place a virtual anywhere. Web Connection creates a custom project structure in an arbitrary folder (`webconnection\projects\yourproject` typically) and you can easily configure for this location. The automated install handles this automatically.

This folder ends up holding all your HTML, CSS and JavaScript resources used by the Web application.

#### Copying the Web Connection DLL
The most important file for the Web installation is the Web Connection ISAPI DLL (wc.dll). If you copied from a template this file will already be there otherwise you can find it here:

```dos
COPY FILE TO "<webconnectionInstallFolder>\templates\scripts\wc.*" TO ;
             "<WebDirectory>\bin\wc.*"
```

This copies both wc.dll and wc.ini configuration file. wc.ini holds Web Connection configuration information in an INI file format and you'll need to confgure this file to match the settings in your server file. Specifically make sure you set the path (which is the temp file path for file based messaging) and the Template which needs to match the settings configured in your Web Connection server (`<yourapp>.ini`)

```ini
;***THIS DIRECTORY MUST HAVE READ AND WRITE ACCESS FOR THE
Path=.\temp

;***Message File Template (1st 3 letters)
;***Default is "wc_"          Only needed if using a different template
Template=WC_
```

The wc.ini file should then have the path specified.

```ini
Path=c:\webconnectionprojects\myapp\deploy\temp
Template=WC_
```

#### Modifying httpd.conf
Apache configuration is handled through the `Apache/conf/httpd.conf` file. In order to create a site or virtual you need to a add a few settings to this file.

At the bottom of the file add the following in `httpd.conf` (example uses an `apachetest` virtual):

```txt
#*** WEB CONNECTION VIRTUAL - apachetest

#*** Only load this once per config file - you'll get a warning otherwise
LoadModule webconnection_isapi_module modules/mod_webconnection_isapi.so

#*** WEB CONNECTION SCRIPT ALIAS
ScriptAliasMatch (?i)^/apachetest/.*\.(act|wc|wcs|wcsx)$ "c:/WebConnectionProjects/apachetest/Web/bin/wc.dll"
#*** END WEB CONNECTION SCRIPT ALIAS

Alias /apachetest/ "c:/WebConnectionProjects/apachetest/Web/"

<directory "c:/WebConnectionProjects/apachetest/Web/">
	DirectoryIndex default.htm index.html
	Options ExecCGI
	Require all granted
	AddHandler webconnection-isapi-handler .dll
</directory>

#*** END WEB CONNECTION VIRTUAL - apachetest
```

> ### @icon-info-circle Case Sensitive
> Apache is based on Unix conventions and as such is **case sensitive**. When you create a virtual directory or any virtual reference paths, make sure the case matches file and folder case on disk. If you got **404 File Not Found** errors, that's first thing to check. I recommend sticking with <a href="https://en.wikipedia.org/wiki/letter_case#special_case_styles" target="top">kebab case</a> (all lower case with dashes (`-`) for separators) when working with Apache.

The comment lines (#) are optional but recommended as they are placeholders for the Web Connection configuration routines that allow you to update the settings using the Wizards or programmatic tools to configure the server. If you add the comments for auto-configuration make sure the comment lines are EXACTLY as above (ie. cut and paste!).

The key features are:

**LoadModule** loads the Web Connection Apache module. This is a global setting and should only be specified once in the file.

**ScriptAliasMatch** is used to deal with script mapping. It routes any custom extension you'd like to configure to the Web Connection ISAPI DLL. NOTE: it's crucial that this directive is declared before the Alias for the virtual is defined - otherwise you will get File Not Found errors on any requests that access non-file backed 'scripts'.

**Alias** creates a Virtual directory. It maps a logical/virtual path to a physical path.

> ### @icon-info-circle Order is Important!
> Make sure you declare `Alias` **after** `ScriptAliasMatch` or scripts will not be found at runtime.

The `<directory>` tag contains configuration settings for the specific directory on disk.

**Options ExecCGI** enables script execution in the directory and allows both the Apache Module and Web Connection ISAPI DLL to execute.

**AddHandler** adds the `webconnection-isapi-handler` and maps the .dll extension to it. Note that script maps (defined in `ScriptAliasMatch`) are mapped to the DLL so it applies to `.dll` files **and** any extensions that are mapped to the DLL.

**Require all granted** gives execute permissions to all extensions that are mapped. Without this setting the extensions can't execute.

### Programmatically setting Configuration Settings
In addition to manual configuration you can also programmatically configure Apache. You can use the wwWebServer Class to perform these tasks:

```foxpro
DO WCONNECT
SET CLASSLIB TO WebServer ADDITIVE

oWeb = CREATEOBJECT("wwWebServer")
oWeb.cServerType = "APACHE"

* **Create a virtual directory 
oWeb.CreateVirtual("WebDemo","d:\westwind\webdemo")
oWeb.CreateScriptMap(".wp","d:\westwind\apachetest\bin\wc.dll","WebDemo")
oWeb.CreateScriptMap(".wpp","d:\westwind\apachetest\bin\wc.dll","WebDemo")

* **Edit the .Config file
lcConfigFile = ADDBS(oWeb.GetWebRoot()) + "conf\httpd.conf"
GoUrl(lcConfigFile)   && Opens in Notepad for editing
```

### Using wwApacheRequest instead of wwRequest
Let's move on to the Web Connection FoxPro configuration of the server. Again this step is automatically performed for you when you create your project with the New Project wizard and choose Apache as your Web server.

Apache returns some server variables differently than IIS does, and so a special subclass of the wwRequest class called `wwApacheRequest` is used to handle these differences. IIS returns some platform specific server variables that are not returned by Apache - but most of these are truly platform specific and rarely used so this should not be a problem.

The `wwApacheRequest` class handles path fixup for requests by explicitly checking paths and trying to automatically fix up the physical path to match the true physical path of a given script.

To use the wwApacheRequest class you can simply assign the `.cRequestClass` in the your main `wwServer` class which is contained in `<yourApp>Main.prg`. The following code demonstrates:

```foxpro
DEFINE CLASS WebdemoServer AS WWC_SERVER OLEPUBLIC

cRequestClass='wwApacheRequest'
...
```

Alternately you can also override this globally using the following flag in your WCONNECT_OVERRIDE.H file:

```foxpro
#UNDEFINE WWC_REQUEST
#DEFINE WWC_REQUEST            wwApacheRequest
```

The former is a little less intrusive and isolates the change to the current application, while the latter can be easier if you need to do this with multiple applications as the flag is global to all Web Connection applications.

### Set the CallCoInitialize flag in wc.ini
Starting with Version 4.50 Web Connetion no longer calls CoInitialize by default on COM requests by default. This fixed a number of issues with IIS as IIS had some issues with multiple calls to CoInitialize and potential mismatched CoUninitialize calls. IIS automatically calls CoInitialize for each thread, so there's no need to do it again. 

**Apache however does not do this**, so we need to tell the Web Connection ISAPI dll to explicitly to call CoInitialize/CoUninitialize. To do so we set the CallCoInitialize flag in the wc.ini file:

```ini
[Automation Servers]
;* **Severloading - 0 - Normal  1 - Round Robin
ServerLoading=1

;* **KeepAlive  0 - Normal  1 - Force extra COM reference to keep alive
KeepAlive=1

Server1=webDemo.webDemoServer
;Server2=webDemo.webDemoServer

; *** SET THIS FOR APACHE!
CallCoInitialize=1
```

Your wc.ini file is found in the same directory as wc.dll which will be your Web virtual directory usually in the BIN path.

### Accessing the Administration Page
Web Connection ships only with ASP.NET 

### Configuring Apache Security for Web Connection
Apache does not support application driven Basic Authentication so Web Connection's default mechanism for accessing admin links and the like won't work. Your only choice to work around this limitation is to configure Apache for directory security and create script files for admin operations in a separate, protected directory.

To work around these issues and provide the basic security for locking down administration functions in the Web Connection DLL, the Web Connection Apache ISAPI Module provides Windows based Basic Authentication. When using the custom module, programmatic Basic Authentication works just like it does in IIS by authenticating against Windows User accounts rather than using Apache's password files.

This lets you set security in the wc.ini configuration file:

```ini
;* **Account for Admin tasks    REQUIRED FOR ADMIN TASKS
;* **      NT User Account   -  The specified user must log in
;* **      Any               -  Any logged in user
;* **                        -  Blank - no Authentication
AdminAccount=Any
```

> #### Apache Security Note
> Web Connection will override security with the custom behavior only if Apache has not provided its own security implementation. If you lock down directories and Apache can authenticate users prior to hitting the Web Connection module, the Apache authentication takes precedence. To avoid confusion in this scenario you should avoid using Basic Authentication outside of programmatic use within your Web Connection applications. Instead choose Digest or other custom security scheme for directory based rights in Apache - this will avoid any conflicts.

Check with your Apache Administrator for more information on setting up Web Server authentication for requests.