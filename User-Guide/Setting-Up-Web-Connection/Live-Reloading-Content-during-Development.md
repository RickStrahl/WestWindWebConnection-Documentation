Live Reload is a new feature in Web Connection that can detect when you make changes to any of these types of files:

* Static HTML files
* Static CSS and JavaScript Files
* Web Connection Scripts and Templates
* FoxPro Code in your Web Connection Server

Live Reload then **automatically refreshes** the browser after a changed file is saved to disk. The automatic refresh is the same behavior as manually pressing the refresh button in the browser, except it happens automatically. Browsers maintain their scroll position on refreshes by default so you easily see pages update **as you type and save (ctrl-s)**.

> #### @icon-info-circle Use for Development Time Only!
> Live Reload is a development time feature and **should be disabled on a live server** both for the Web Server (IIS or WC Web Server) and the Web Connection Server. For more info on enabling and disabling see below.
>  
> Note also that the Web Server based live reload is not useful for REST Services - only the Web Connection Server code re-compile is useful in that scenario. Web Server Live Reload makes sense only for HTML based interfaces that are not controlled by an external framework and tooling (ie. Angular, VueJs, React, Svelte CLIs etc.)

Here's what Live Reload looks like in action:

![Live Reload in Web Connection](https://github.com/RickStrahl/ImageDrop/raw/master/WebConnection/LiveReload.gif)

This walk through demonstrates:

* Web Connection Script page changes <small>(page just reloads)</small>
* CSS Changes <small>(page just reloads)</small>
* FoxPro Server Code Change <small>(server restarts, page reloads)</small>
* Fixing a FoxPro Code Error <small>(server restarts, fixed page reloads)</small>

This allows you to display your editor and your browser side by side, and as soon as you make a change you see the live updates in the application, almost in real time. It's like WYSIWYG on steroids, excepts it's **actually running your real live application**.

> **It's a very powerful feature, that can improve productivity drastically.**

To take full advantage of Live Reload, you'll want to use a decent editor in combination with it. Preferably you'll want to use an editor that can handle both HTML, CSS and JavaScript and also your FoxPro code files for quick fixes. You may still want to use the FoxPro editor for larger code changes as it has better code completion support, but for quick fixes an external editor is recommended (more on why below).

> Note that in order to make code changes to PRG files and have those changes recompile immediately, you need to use an editor other than the FoxPro editor. The FoxPro editor locks the active file, and won't allow reading of the file while open in FoxPro. In order to effectively make iterative changes that let you keep the editor open while running your code, use an external editor like [Visual Studio Code](https://code.visualstudio.com/) or similar.

## When to use Live Reload

### Requirements
Live Reload works with:

* Full IIS
* IIS Express
* Web Connection Web Server

### Live Reload Types
There are two parts to Live Reload in Web Connection:

* **Web Server Reload**
This reloads the Web Page when HTML, CSS, JS and Web Connection scripts/templates on the client side have changed. The Web Server interface uses WebSockets to communicate with the open Web page to tell it to refresh after changes to these client centric files.

* **Web Connection Server Reload**  
This reloads the Web Connection server application after a server code change. This lets you make changes to PRG files and automatically restart the server with the changes applied.


For HTML based application you typically use both in unison. For REST services where you don't manage the HTML interface or use another tool like a CLI that includes it's own local Web Server, you only want to use the server Live reload.


### WebSockets and IIS 
IIS requires that **WebSockets** functionality is installed:

* Make sure the **WebSockets Feature** is installed from IIS Features

[Web Connection's IIS install script](https://webconnection.west-wind.com/docs/_22f0xkbmq.htm#powershell-iis-feature-installation) automatically installs the WebSocket feature, so this affects only older installations or explicit manual installations.

### Latest WebConnectionModule.dll and Microsoft.WebSockets.dll
Live Reload is new in 7.10+ and depends on WebSockets functionality and the .NET Handler uses an external dependency in the form of `Microsoft.WebSockets.dll` which is a **required dependency** to the `WebConnectionModule.dll`.

* Make sure you have a current `/web/bin/WebConnectionModule.dll`
* Make sure you have `/web/bin/Microsoft.WebSockets.dll`

### Web Connection Web Server
The Web Connection Web Server is a self contained Web Server that ships with your Web Connection project. Each project is created with its own private copy of this generic Web Connection Web Server.

The Web Server automatically has WebSocket and Live Reload support so nothing else to do there.

The Web Connection Server is built ontop of **.NET Core** and so it **requires the .NET Core Runtime or .NET Core SDK** in order to work. 

* [Install .NET Core Runtime or SDK](https://dotnet.microsoft.com/download)

For a local development machine the SDK is recommended. For a server, the .NET Core **Hosting Bundle** (which includes the runtime and ASP.NET Core bundles) is sufficient to run Web Connection applications.

## Enabling Live Reload
There are two places where Live Reload needs to be enabled:

* The Web Connection Web Server Module Configuration <small>*web.config or WebConnectionWebServerSettings.xml*</small>
* Web Connection FoxPro Server Configuration <small>*yourApp.ini*</small>* 


The former handles updates to static Web files and Web Connection scripts and templates - basically anything in the Web folder that changes. The latter handles FoxPro code changes which in turn trigger the FoxPro Web Connection server to restart after a code change.

### Enabling Live Reload in the Web Interface
The Live Reload features of the Web interface apply to static files and Web Connection script files.

The easiest way to enable Live Reload is through the Web Server administration interface in `Administration.wc`:

![](/images/stepbystep/EnableLiveReloadWeb.png)

This link toggles the feature on and off **on the Web Server** and also tries to toggle the setting in the FoxPro server (the FoxPro server has to be restarted to see the change though).

This change corresponds to the following configuration settings:

In **web.config** (.NET Handler)  
```xml
<add key="LiveReloadEnabled" value="True"/>
```

In **WebConnectionWebServerSettings.xml** (.NET Core Web Connection Server)  
```xml
<UseLiveReload>true</UseLiveReload>
```

> #### @icon-info-circle Running in IIS: Disable Live Reload Completely
> I you are planning to run the Web Connection server in Production inside of IIS, you should completely disable Live Reload by adding the following environment variable to your `web.config` in 
[the `<aspNetCore>` Section of the configuration](https://webconnection.west-wind.com/docs/_5lw0ysxq9.htm#iis-requirements-and-basic-configuration).
> ```xml
> <environmentVariable name="WEBCONNECTION_USELIVERELOAD" value="False" />
> ```


### Enabling Live Reload in the FoxPro Web Connection Server
The Live Reload features in the Web Connection server apply to code changes made in the Web Connection FoxPro application code. Any code change in FoxPro code files in the `Deploy` folder triggers the Live Reload to restart the Web Connection Server.

If you use the Web Server link above, and standard project naming conventions then the settings in the `WebDemo.ini` file likely will be updated automatically.

```ini
LiveReloadEnabled=On
```

Restart the FoxPro server (if it doesn't automatically restart).

## Running with Live Reload
Once Live Reload is enabled any change to an HTML page, CSS, JavaScript or Web Connection script causes the Web Browser to refresh the actively loaded page. The page position is saved whenever possible.

You can also make code changes in your FoxPro code - but you don't want to stop the running Web Connection Server. Ideally you'll make changes in an external editor (or a separate non-executing FoxPro instance) to minimize the amount of effort in making a change.

There's really nothing to it.

## Live Reload Tips and Tricks

### @icon-lightbulb-o Run your Server in Non Debug Mode
Normally when you debug your application you want to run the server in Debug mode so that the server stops on a failing line of code. This is great for debugging and stepping through code or simply fixing a problem at the source.

When using Live Reload however, you **don't want the server to stop**. Rather, it's much better to let the server fail **and display an error message**, which you can then use to fix the problem **in an external editor**.

This allows for the quick run, fail, edit, run, fail edit, run cycle - known as the *'inner loop'* that gives you high productivity. The idea is to fail fast, fix fast - and Live Reload picks up the change immediately to refresh the active page - by the time you look at the page it's probably already up and running with the update.

Most code failures are typos or simple logic errors that are easy to discern from an error message and pointer in the code so this non-debug, Live Reload cycle is very fast for fixing basic errors.

If you run into a thorny problem that you can't discern just from error messages and looking at code, you can turn the server back into Debug mode. You can then again fail on failure and continue to step through the code and use the debugger as you would n the past.

### @icon-lightbulb-o Use an Editor other than the FoxPro Editor
The FoxPro code is problematic for editing FoxPro code, in that it locks the files while the editor is open. FoxPro in turn won't compile a PRG file if it's locked, so any code change requires that the file gets closed. Also using the FoxPro editor usually means stopping the Web Connection server - which with Live Reload is not something you want to do. 

I can recommend using [Visual Studio Code](https://code.visualstudio.com/) with the [FoxPro language addin](https://marketplace.visualstudio.com/items?itemName=NewDataSystems.foxpro) which allows you to edit text with syntax coloring, save **and leave the document open** in the editor. If you need to make another change the editor is already open at the correct cursor position, and ready for the next edit.

### @icon-lightbulb-o Use or Configure the Server's Edit Button
The server form includes an Edit button on the main form, which when clicked opens up an editor of your choice in the project directory. 

![](/images/stepbystep/ServerFormEditButton.png)

By default this button is configured to use Visual Studio Code - if that's installed on your machine the Edit button just works.

![](/images/stepbystep/VisualStudioCodeEditorWebDemo.png)

But Visual Studio Code is optional. If you want to use another editor you can do that too. Ideally you'll want an editor that can open a folder, so as above you can open the entire project folder that holds both the FoxPro code and the Web code.

The value is configurable in `WebDemo.ini` and the `CodeEditorCommandLine` key:

```ini
# WebDemo.ini
[Main]
Codeeditorcommandline=code ..\
```

Substitute a command line that can launch your editor of choice. Note `code` is `code.exe` which is installed on the Windows path. if you use another editor you may have to specify the full path to that editor and provide a full path to the command argument as well.



## Updating an older Web Connection Module Installation
New Web Connection projects automatically create all the required settings in the newly created project and the `web.config` (.NET Handler/Module) and `WebConnectionSettings.xml` (Web Connection Web Server). But if you're updating an older application running the .NET Handler you'll have to add a few things manually.

When updating the .NET Handler two things have to be set in `web.config`:  

* The `WebConnectionModule` module has to be assigned
* The TargetFramework has to be set on the `<compilation/>` element

### WebConnection Module
The Web Connection module is new in v7.10 and provides support functionality to the .NET Handler that processes requests. This new module is basically responsible for checking for `.htm` and `.html` pages and handling them as pages that can be live reloaded automatically.

To set this up add the following to the `<modules>` section:

```xml
<modules>
  <add name="WebConnectionModule" type="Westwind.WebConnection.WebConnectionModule,WebConnectionModule"/>
</modules>
```

The module enables LiveReload in .htm and .html files.

It replaces the old `.htm*` handler mappings which should be removed if you have them. If you have the following entries remove them:

```xml
<handlers>
    ... 
    <!-- remove this! -->
    <add name=".html_wconnect-module" 
         path="*.htm*" verb="*"
         type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" ... />
</handlers>     
```

### Set .NET TargetFramework on <compilation>
If you're upgrading from an older version, also make sure that the targetFramework is set at least to version `4.6.1`. This setting is set in the default template but may have to be set for older applications:

```xml
<system.web>
    <compilation defaultLanguage="c#"  targetFramework="4.6.1" />
    ...
</system.web>
```

This setting is **required** in order for WebSockets to work as this feature relies on async features that are new post .NET 4.5. The minimum required .NET Version is `4.6.1` but I recommend setting this value to the actual version of .NET on your system - which likely is `4.7.2` or `4.8`. Later versions provide additional internal .NET feature updates that improve overall .NET runtime performance.