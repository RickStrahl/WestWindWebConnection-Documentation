Web Connection is updated frequently and so you'll often have to update an existing installation with the core files from a new version. This topic refers to same version updates (ie. 7.10 -> 7.12 for example).

> #### @icon-warning Updating from the Shareware Version
> If you are updating from the Shareware version or you had previously installed the Shareware version, or you have already installed and are seeing shareware version prompts we recommend a **full delete of all files** in the Web Connection installation folder followed by a full reinstall as a **Fresh Install** as described below.

### Do a Fresh Install!
Web Connection 6.x and later uses a new project system that **doesn't write anything of interest into the Web Connection Install folders** after installation. So the installation folder doesn't have any files worth keeping between releases and it is safe to delete the installation files and reinstall.

For this reason **a complete delete all and reinstall is the preferred way to update a Web Connection installation now.**

> You can install Web Connection anywhere you like, but the following assumes `\wconnect` as the install folder.

If you build applications using the new project system **all application related files will be in your project folders**. So it's perfectly safe to:

* Backup your existing installation to something like `\wconnectXXX`
* Delete all content in the original `\wconnect` folder
* Reinstall or Unzip Web Connection into `\wconnect`

There are potentially some files that you may have customized and you may want to copy back from your old install:

* `wconnect_override.h`
* `wwSession` and `WebRequestLog` tables if you need to retain logs/sessions
* Any other files you've explicitly put in the install folder **(not recommended!)**

> Always, always put your application specific files into a separate project folder.

Once copied we recommend you also run `setup.exe` again, to update the Visual Studio Addin and Visual Studio and Visual Studio Code templates etc. but this is optional. Setup doesn't touch any of your project files so it's safe to run without breaking existing applications. 


> #### @icon-info-circle If Things Go Wrong
> If things don't work out with the new version and you have breaking changes that you don't want to fix, you can easily **roll back** simply by changing your FoxPro path to point at the **old** Web Connection folder (ie. `\wconnectXXX`). In your `config.fpw` (or in `SETPATHS.prg`) just use `\wconnectXXX\classes;\wconnectXXX` instead of `\wconnect` to point at the old folders.


### In Place Update
If you have an older version of Web Connection, or you frequently store custom files in the Web Connection folder you may prefer to do an in-place update. So rather than nuking all the files you'll essentially copy the new version ontop of the old version.

Run the registered version installer and choose the existing installation folder (`\wconnect` by default). Simply unzip the new files into your old installation directory.

Note that if you've modified any of the samples files, or any of the framework files you won't want to update the existing installation or at least make sure you do a backup prior to installation of the new version. In general doing a back up of the old install is probably a good idea.

After you've installed the new files over the existing installation, make sure you:

* **Delete all FXP files in the project directories**
* **Recompile all PRG and VCX files**
* **Update DLLs in production apps**
* **Update the Web specific files (DLLs, Scripts,CSS)**

### Recompiling
To recompile all code files to the latest version is necessary to ensure that both the FXP files as well as your project is recompiled. You can do the following from the VFP Command Window in the Web Connection startup directory:

```foxpro
CLOSE ALL
CLEAR ALL
DELETE FILE classes\*.fxp
DELETE FILE *.fxp
COMPILE classes\*.prg
COMPILE CLASS CLASSES\*.vcx
```

You can also use:

```foxpro
DO CompileWC()  && in '\tools' folder
```

#### Shareware Version Update 
If you are updating from the Shareware Version and you're overwriting make sure you:

* Delete `wconnect.app`
* Make sure that `#DEFINE SHAREWARE .F.` in `wconnect.h`
* Ensure you recompiled **everything** as described above

### Update DLLs in Production Apps
Web Connection 7 and later uses self-contained projects to hold all resources associated with a Web Connection project - nothing gets stored in the Web Connection install folder, and all support files other than the Web Connection code dependencies are contained within the project's folder hierarchy.

### Use the Update Project Wizard (or Console Command)
The easiest way to update projects is to use a new utility in Web Connection 7.12+ which lets you update existing projects. There are two ways to do this:

* Launch the Web Connection Console (`DO Console`)
* Choose **Update Project Resources**
* Pick a project folder
* Choose components to update
* Finish

![](/images/ManagementConsole/UpdateprojectResources.png)


Alternately you can use a Console Command like this:

```Foxpro
DO CONSOLE WITH "UPDATEPROJECTRESOURCES", "\WebConnectionProjects\SecurityTest","modules,dlls,libs,scripts,css,views"
```

This updates the following:

* **modules** - Web Server Integration Module Dlls
* **dlls** - Support DLLs for the FoxPro Server (wwdotnetbridge and .net helpers)
* **libs** - Web JavaScript and CSS libraries (bootstrap, fontawesome etc.)
* **scripts** - Web Connection specific support scripts (ww.jquery.js)
* **css** - project specific default CSS file: application.css
* **views** - project specific Views (be careful since these you might have changed - back them up)

### Manually Updating
The `UpdateProjects` tooling makes it easy to update relevant components. The following are more details on what is actually updated.


### FoxPro accessed DLLS
Web Connection uses a number of DLL files that are distributed in the Web Connection root folder. These files are required in deployed applications and should match the versions that you are running during development.

> #### @icon-warning Make Backups before Updating
> Always back up or commit your current application's changes to source control before updating files so you can roll back or copy the old files if necessary.

The DLLs in questions include:

#### Core Dependencies
* wwIPstuff.dll
* wwDotnetBridge.dll
* Newtonsoft.Json.dll
* zlib1.dll

#### General Purpose
* dzip.dll, dunzip.dll
* wwImaging.dll

#### .NET Interop 
* Markdig.dll  <small>*(markdown)*</small>
* Renci.SshNet.dll   <small>*(sftp)*</small>
* FluentFtp.dll    <small>*(ftp, ftps)*</small>

Of these files **wwIPStuff.dll**, **wwDotnetBridge.dll** and **zlib1.dll** are core dependencies that **have to be present** as they are used internally in Web Connection. 

**Newtonsoft.Json.dll** is required for all JSON related operations including the [wwJsonSerializer](VFPS://Topic/_1WU18OWBA), [REST Services](VFPS://Topic/_4IU1EV8PM), [wwJsonServiceClient](VFPS://Topic/_4JF1F19ZR) and anything else your code might use to parse JSON.

The remainder of files are specific use cases but I recommend you copy and update them nevertheless. 

> We recommend you copy all the DLLs from the Web Connection install folder to your live application deploy folder. You can also use the **build.ps1** file in Web Connection projects to create a `Build` folder that includes your binary files along with the Web Connection required dlls in the `\deploy` folder.

### Web Server Integration Modules
These are the Web Server connectors that connect the Web Server to the FoxPro Web Connection Server. That's the ASP.NET Handler, the old ISAPI module, and the new Web Connection Web Server. 

These files are copied from `\Templates\Scripts` into `\web\bin`.

### Copy Web Support Files
You'll also want to update your Web directory support files that provide support images and  JavaScript files for the current version. These files should be updated in the Web folder of each application.

Next update scripts, css and Page Templates (selectively): 

To do this copy the following:

**Always Update** (unless you manually manage these)

* templates\ProjectTemplate\Web\lib -> Web Dir\lib
* templates\ProjectTemplate\Web\scripts -> Web Dir\scripts* 

**Conditional Update**  (Update if you don't change default themes/layouts)

* templates\ProjectTemplate\Web\css -> Web Dir\css
* templates\ProjectTemplate\Web\Views -> Web Dir\Views


The first set are core libraries that are used by Web Connection internally to display administration pages, stock startup, information and error pages as well as the default Layout templates. You need to update these libraries to keep them up to date.  
*These files include libraries like jQuery, BootStrap and FontAwesome as well as some Web Connection support script files.*

The Conditional Updates are optional, because it's quite likely that the files in these sets may have been modified as part of your application. 
*These files include the default css formatting that Web Connection uses, as well as the default layout templates in the `Views` folder*



> **Important**: Always create a backup first and then use a comparison tool like <a href="https://www.scootersoftware.com/" target="top">Beyond Compare</a> or [WinMerge](http://winmerge.org/?lang=en) to check for changes when in doubt.

> **Important:** If you've modified any of the files, they will be overwritten. Especially make sure that you rename `application.css` in your application to something that is specific to your own application - something like `myapp.css` and then link that in your layout files.

### Update Project Generated Files
When you create a new project Web Connection generates a number of files which is a one time process. These files are generated from templates and these templates occasionally change, so from time to time it's a good idea to check and see what has changed.

A good recommendation if you would like to update these files is:

If you have older projects and you want to update to the latest support files I recommend:

* Create a new project into a new folder
* Use [BeyondCompare](https://www.scootersoftware.com/) or similar to compare files

You'll want to compare at the following files:

* build.ps1
* install-iis-features.ps1
* deploy\bld_YourApp.prg
* deploy\yourApp_serverConfig.prg
* deploy\yourAppMain.prg