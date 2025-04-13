Once you've developed your application and you've tested and debugged it on your development machine, the next step is to transfer that application to the server for deployment. 

### Summary
Deploying involves a number of steps:

* Make sure IIS is installed and base configured
* Install the Visual FoxPro runtime files for an EXE based COM server
* Copy your application project folder (Deploy folder)
* Copy your Web Files to the server (Web Folder)
* Copy any Data for your Application (where ever)
* Run Web Server Configuration Tool
* **Or** manually configure your Web site/virtual
* Set permissions on folders

### Visual FoxPro Runtimes
In order to run your FoxPro application you need to have at least the FoxPro Runtimes installed. You can install the FoxPro runtime from the VFP download location by going to the following link and clicking on **Download**.

* [Download Visual FoxPro 9.0 Runtimes from VFPX](https://github.com/VFPX/VFPRuntimeInstallers/blob/master/VFP9SP2RT.exe)

As an alternative you can also install a full install of FoxPro on your server. Sometimes this can be useful if you need to manually manipulate FoxPro data files or you need to debug your application. Y

### Server Configuration
Before anything else, you need to make sure your Web Server is ready to run your Web Connection application. Two things need to happen to make that work:

* **IIS has to be installed and configured**  
Since Web Connection runs on IIS (or also Apache) we have to make sure that IIS is installed. Follow the [IIS Installation instructions](VFPS://Topic/_22F0XKBMQ) to install and configure the base Web Server installation.

* **Visual FoxPro Runtimes have to be installed**  
Since a Web Connection server is a Visual FoxPro 9 application, you'll need to install the Visual FoxPro Runtimes on the Web Server. Either install a full version of Visual FoxPro on your server, or install a runtime only build. You can download a pre-configured runtime installer from <a href="http://www.foxpert.com/runtime.htm" target="top">Christof Wollenhaupts FoxPert Web site</a>.

### Simple Copy
Because Web Connection projects contain a single folder hierarchy, you pretty much can zip up your entire application folder tree (`deploy` and `web` folders) and push that up to the server and unzip there. 

#### Build.ps1 helps packaging for files
If you are using a **new project** in Web Connection 6.0 or later you can also use the `build.ps1` file in your project's root folder to build all the distribution files into a self-contained Zip file you can copy to your server.

The batch file copies all the required Web Connection dependency DLLs alongside your exe and startup INI files. 

It creates:

* `deploy` folder
* `web` folder
* `data` folder (if it has something in it)

You can optionally add additional folders to this zip file as needed.

The following section describes how to manually copy these files.

### Copy Dependency DLLs into Deploy
However, one thing missing from such a simple folder copy, is the collection of dependent DLLs that Web Connection uses. These DLLs live in Web Connection install folder but need to be copied into the `Deploy` folder before you build your final install for the server:

* wwipstuff.dll
* wwdotnetbridge.dll
* zlib.dll
* Newtonsoft.Json.dll
* Markdig.dll
* Renci.SshNet.dll

The simple install often is sufficient but if your project is more complex or you want to break up your Code and Web folders into separate repeatable packages, you can use the following steps.

New Web Connection projects include a `build.ps1` file in the root folder that create a combination of your `deploy` folder, plus the required DLLs in a generated `build` folder which is suitable to deployed to a live server.

### Package your Deploy Folder
Your Web Connection project by default depends on the Web Connection installation for various dependencies and DLL files as outlined in the previous section. 

From Web Connection's perspective, the deploy folder typically only needs a few files:

* MyApp.exe
* MyApp.ini
* `\Temp` Folder (empty)
* wwipstuff.dll
* wwdotnetbridge.dll
* zlib.dll
* Newtonsoft.Json.dll
* Markdig.dll
* Renci.SshNet.dll

Add to that any explicit dependencies that your application has if any. Extra configuration files. Data Files etc.

To help with collecting dependency files and to build a repeatable, deployable `Deploy` folder, a `build.ps1` file was created in the project root. When you run this batch file it'll copy all the dependent support files from the Web Connection installation folder, plus your compiled EXE and INI files into a `Build` folder. The batch file also zips up all those files for you (if 7zip 7z.exe is on your path).

If you have additional files or folders you need to deploy as part of your application I recommend you add those to the build folder or better yet you add the commands to copy the files in `build.ps1` to copy them as part of the script that creates the final `\build` folder. You can zip up all files in this folder, upload to the server and then unzip them back into the `Deploy` folder.

### Copying your Deploy Folder to the Server
Once you've zipped up all of your code files you can send those to the server and make sure to unzip them into the `Deploy` folder of your project. Keep in mind that the project structure is important so the `Deploy` and `Web` folder names are significant. 

Depending on how you packaged your code files you likely just unzip the file into your `Deploy` folder after which you should be ready to run your main EXE file.

> #### @icon-info-circle Web Connection Handler File Upload
> The Web Connection handler includes <a href="http://west-wind.com/webconnection/docs/_s8x08h3ks.htm#uploadzipfile" target="top">a file upload option</a> that allows you to upload arbitrary files to the Web server, which can be a great way to push files the server without requiring installation of an FTP server. For this to work you first have to publish your Web Folder so the module is present (see steps below) and you'll need to modify the .config file to allow large file uploads.


### Copying Web Folder to the Server
For deploying your Web files (Html, CSS, JavaScript, Images and Web Connection Script Files) to the server you have a number of options:

##### Simple Copy
At its simplest you can simply zip your `Web` folder and send that up to the server and unzip it there. That works fine for a one time install. You can use Remote Desktop File Transfer, a DropBox/OneDrive file share, or any number of other ways to the get files up there.

##### Using Visual Studio Web Publishing
However, more than likely you'll also want to update Web files frequently as you modify your application and for that you typically will want to use an FTP server or IIS Web deploy to allow for individual or incremental updates of server files.

If you use Visual Studio, you can use the **Web Publish feature** and **Web Deploy** on the server to push files from your machine to the Web server. Web Publishing uses either FTP or <a href="http://www.iis.net/downloads/microsoft/web-deploy" target="top">IIS Web Deploy</a> to publish your entire Web site **or** individual files or folders to the Web server. Web Publish supports incremental updates of projects as well as allowing to publish individual files quickly and easily right out of Visual Studio.

> #### @icon-warning Exclude for PRG/FXP Script Files
> If you're using Web Connection Scripting Pages (via `Response.ExpandScript()`) make sure you don't publish your PRG or FXP files in your Web folder. When script files compile they embed hardcoded paths into the PRG and if paths change the code will break. Either let the files auto-compile or if you're running pre-compiled scripts use the admin page *Compile scripts* option to compile your scripts.

### Run the Server Configuration Script
Once you've uploaded and set up  your `Deploy` code and `Web` files, you need to configure the Web Server for your application. Make sure that [IIS is installed and configured on the server](VFPS://Topic/_22F0XKBMQ), and that any Web site you plan to install on exists before you start.

Then simply run the following from an Administrator Command Prompt:

```
MyApp.exe CONFIG
```

where `MyApp` is the name of your server application.

> #### Configuring  the Deployment Script
> If configuration of your server is different than your local dev environment - different virtual/root setup or different folder structures - you'll need to adjust the configuration for the script which is contained in the `YourApp.ini` file setting next to `YourApp.exe`. 
>
> ``` ini
> [ServerConfig]
> Virtual=wconnect
> ScriptMaps=wc,wcs,wcsx,wwd,wst,md,blog
> IISPath=IIS://localhost/w3svc/1/root
> ```
>
> You can change the Web Site by changing the IISPath and the ID (the number before `/root`) and matching that to an IIS Site id you can find in the IIS Manager. You can change the virtual folder name, or leave `Virtual` empty to specify a root Web site.


### Manual Configuration
If for some reason the automatic configuration does not work, or you're running an older pre-v6 server you'll need manually configure the server. To do this you need to perform the following steps:

* Create an IIS Application Pool (<a href="http://west-wind.com/webconnection/docs/_22f0xkbmq.htm#createanapplicationpool" target="top">see</a>)
* Create the IIS Virtual Directory using the Application Pool (<a href="http://west-wind.com/webconnection/docs/_22f0xkbmq.htm#virtualdirectoryconfiguration" target="top">see</a>)
* Add Windows and Basic Authentication (<a href="http://west-wind.com/webconnection/docs/_22f0xkbmq.htm#virtualdirectoryconfiguration" target="top">see</a>)
* Create ScriptMaps for `.wc` and `.yourExt` (<a href="http://west-wind.com/webconnection/docs/_22f0xkbmq.htm#creatingscriptmappings" target="top">see</a>)
* Create Temp Folder Permissions (<a href="http://west-wind.com/webconnection/docs/_22f0xkbmq.htm#tempfilepathpermissions" target="top">see</a>)
* Lock down the Admin Folder

For the FoxPro Application:

* Register your EXE as a COM Server with `MyApp.exe /regserver` (**Admin** required)
* Copy data directories and make sure paths are referenced

### Fix up any custom Configuration Setting
By default your Web Connection configuration should be complete at this point and you should be able to run your application.

If you have any custom configuration settings that depend specifically on the environment - email servers, passwords, special paths etc.-  in your configuration files you should make those changes now.

You should also set these [settings for your production server](VFPS://Topic/_6D218U9C8):

* `DebugMode=off`
* `UnattendedComMode=on`
* `LiveReloadEnabled=off`

These changes typically affect `MyApp.ini` for your FoxPro server configuration, and `web.config` for the Web Connection handler configuration with the latter rarely having to change.

### Start up the Application
At this point your server should be ready to run. I recommend you start by testing your application in **File Mode** first. Make sure the the `MessagingMechanism=File` to start.

To test, run your server by simply double clicking the EXE in Explorer or running from the Command Prompt. If all goes well the server should just pop up.

> If you manually configured or aren't using a new-style project layout, make sure your temp path, template and all the paths in `yourApp.ini` and `web.config` (or `wc.ini` if using ISAPI) match. Specifically make sure the `TempPath` and `TempFilePrefix` match.

Next you should navigate to the Web Connection Administration Web site and go to the .NET Handler page:

```
http://MySite.com/MyApp/ModuleAdministration.wc
```

and just verify that the settings there look OK. If you had your development environment set to COM you might want to switch to file based to verify that the server works first.

Hit any of your application links that use your custom script map.

> #### @icon-info-circle If you can't log on to the Admin Page from the Server
> Windows Server by default locks down local logins using `localhost` or any other local IP Address which means you can't authenticate using Windows Authentication. To work around this log on from a remote machine or follow [the steps outlined in this help topic](VFPS://Topic/_4GI0QL5JB).


##### Check User Identity of your Application
Pay special attention to the Security section on the bottom of the .NET Handler Admin page, and verify that user account the server runs under is what you want to use. By default this should be **SYSTEM** which is determined by the User Identity on the IIS Application Pool. If you want to use a different user account you need to change the <a href="http://west-wind.com/webconnection/docs/_22f0xkbmq.htm#settheaccountidentity" target="top">Application Pool User Identity in the IIS Management Console</a>.

And you're good to go! You're application should be ready to run!