[IIS Express](https://www.microsoft.com/en-us/download/details.aspx?id=48264) is a small, downloadable, standalone version of IIS. It has its own localized setup for each Web site so there's no global configuration, and most importantly **doesn't require Administrator rights** to configure or run. IIS Express runs the same codebase as IIS 10 (or 7) but runs as a **standalone application** that is manually started.

### Quick Start

* <a href="https://www.microsoft.com/en-us/download/details.aspx?id=48264" target="top">Install IIS Express 10</a> <small>(Windows 11, 10, 8, 7)</small> or <a href="https://www.microsoft.com/en-us/download/details.aspx?id=1038" target="top">IIS Express 7.5</a> <small>(XP/Vista)</small>
* Launch IIS Express from FoxPro
* Navigate to http://localhost:7000 (or whatever port you pick)

#### Launching with `launch.prg`
IIS Express support is built into the Web Connection project launcher by default, so the easiest way to launch a Web Connection v7.10+  site in IIS Express is to use the `Launch.prg`  command:

```foxpro
launch("IISEXPRESS")
```

If you created your project or installed Web Connection with IIS Express as your default you can also just use:

```foxpro
launch()
```

since in that case IIS Express is your default

#### Explicitly launching IIS Express with the Console
This assumes you're running a 7.x project or Web Connection root install which includes a generated `launch.prg` file that includes support for running IIS Express on port 7000. You can change the default behavior by changing the `launch.prg` script.


To explicitly launch IIS Express in a specific folder from FoxPro you can use any of the following:

 ```foxpro
*** Launch IIS Express in your Web Folder on port :7000
DO console WITH "IISEXPRESS","c:\webconnectionprojects\WebDemo\Web\","7000"

*** Brings up the IIS Express Launcher
DO console WITH "IISEXPRESS"
```

The second and third parameters are optional and if not specified assume you want the `..\Web` folder relative to your current `.\Deploy` folder in a project, and port `7000`.

You can also bring up a form that lets you pick the path from the **Web Connection Menu** and using **Start IIS Express Web Server**.

Read on for more detail and more execution and installation options.

### Why IIS Express
* Doesn't require a full installation of IIS
* Can run without the need for administrative privileges
* Must be manually started
* Only works against the folder/site you point it at
* Doesn't expose remote connections by default
* Runs on all Windows clients, including Home and Starter editions
* Is a full featured implementation of the full IIS Server functionality
* Is a small downloadable package that installs quickly (<5 megs)
* Works with Web Connection's .NET and  ISAPI Handlers

In short it's a good choice if you don't have Administrative rights on your machine, or you don't want to manage or run a full version of IIS on your machine while still getting all of IIS' features for the site.

### Download IIS Express
IIS Express is a simple and small downloadable package you can grab from Microsoft from this URL:

**IIS Express 10 (Windows 11, 10, 8, 7)**  
<a href="https://www.microsoft.com/en-us/download/details.aspx?id=48264" target="top">https://www.microsoft.com/en-us/download/details.aspx?id=48264</a>

**IIS Express 7.5 (Windows XP,Vista)**  
<a href="http://www.microsoft.com/en-us/download/details.aspx?id=1038" target="top">http://www.microsoft.com/en-us/download/details.aspx?id=1038</a>

> #### @icon-info-circle IIS Express installed with Visual Studio
> If you're using any version of Visual Studio and install the Web payload IIS Express is already installed.

### Launching IIS Express for Web Connection
To do development with the IIS Express Web Server takes two steps:

* Launch IIS Express
* Navigate to the local server's root site URL (http://localhost:8080/ for example)
* You're off and running

IIS Express is a standalone Windows executable. Once installed it can just be started via command line. The Web Connection menu includes a convenient shortcut helper that allows you to launch the IIS Web Server and specify the parameters needed to launch IIS Express:

![](///images/launchiisexpress.png)


When you click the menu option a small launcher form pops up:

![](///images/launchiisexpressform.png)

which you can launch with:

```foxpro
DO CONSOLE WITH "IISEXPRESS", "UI"
```

On this form you can point at the Web root folder that holds your HTML files and a numeric port number that the server will run on. By default IIS Express uses port `8080`, but you can use any HTTP port you like. Web Connection sets the default port to port `7000` (because 8080 is often used by other applications and proxies), but again you can change that.

You can also launch IIS Express directly with the Console from within FoxPro:

```foxpro
DO CONSOLE WITH "IISEXPRESS","c:\webconnectionprojects\usersecuritymanager\web",7000
```

or if you want the interactive dialog to come up:

```foxpro
DO CONSOLE WITH "IISEXPRESS"
```

You can also run these from the WIndows Command line in the Web Connection install folder by running `Console.exe`.

### Accessing the Web Site
Once launched IIS Express essentially runs each folder you point to as a distinct Web site.  To access the site's root folder and default page type:

**http://localhost:7000/**

To access any Web Connection links simply reference them directly in the root folder. For example to run a Web Connection sample page use:

**http://localhost:7000/TestPage.wwd**

> #### @icon-info-circle Use Scriptmaps
> Note that you should **always** use script maps with IIS 7-10 rather than accessing wc.dll directly which is not supported by default in IIS7-10. IIS Express does not support calling the wc.dll directly.

It's recommended that with IIS Express 7 through 10 you use the .NET Managed Handler (webconnectionmodule.dll) rather than the ISAPI DLL (wc.dll) and that you ALWAYS use script maps. Both handler and ISAPI manager are functionally equivalent when used with script maps, but the managed handler is optimized for IIS 7/10's integrated pipeline that mixes native and .NET code. 

For more information, check out the [topic on the Managed Handler](vfps://Topic/_22Q0YKDNS).

### Monitoring running IIS Express Sites and Configuration
You can find more information about IIS Express running and the sites that are actively running in it in the Task Tray. An IIS Express icon lurks in the Task Tray which when clicked shows a summary of each site along with links to each of the site and their site configurations.

<img src="IMAGES/TaskTrayIcon.png">

You can expand the icon and see a summary of sites:

<img src="IMAGES/IISExpressBrowseSites.png">

### Configuring IIS Express
When you create a site for IIS Express it creates a full IIS application instance including its own ApplicationHost.config file which holds the full site configuration. Site configuration holds some important information, such as authentication methods supported, timeouts, filters and so forth.

To find the configuration file associated with IIS Express bring up the Show All Applications link on the System Tray menu for IIS Express. When you do you'll see this dialog:

<img src="IMAGES/IISSiteConfig.png">

Note the link to the Configuration file. You can click the link to edit the configuration file.

### Enable IIS Express Authentication
One missing feature of the default IIS Express install is that it doesn't include any authentication schemes as configured. To enable Basic and/or  Windows Authentication you have to edit the ApplicationHost.config file as follows:

* Open the Config file shown in the figure above
* Search for basicAuthentication and/or windowsAuthentication
* Set the enabled attribute value to true for at least one of the two

This is necessary in order for authentication to work on the admin links.

### Running IIS Express from the Command Line
IIS Express is essentially launched via a Windows commandline. The Web Connection IIS Express launcher form that pops up is merely a wrapper form that creates a command line and then launches IIS Express. 

However if you prefer you can launch IIS Express yourself. You can find out more about the IIS Express Command Line here:

**<a href="http://www.iis.net/learn/extensions/using-iis-express/running-iis-express-from-the-command-line" target="top">Running IIS Express from the Command Line</a>
**

Web Connection typically launches IIS Express like this (all on one line):

```text
<program files>\IIS Express\IISExpress.exe  
                /path:"c:\westwind\wconnect"  
                /port:7000
                /systray:true
```

While this will certainly work, we still recommend you use the CONSOLE as it creates a custom `applicationhost.config` to support all of the features that Web Connection requires, rather than running with default settings.