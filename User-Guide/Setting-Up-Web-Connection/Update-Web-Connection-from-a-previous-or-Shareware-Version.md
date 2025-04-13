To update a Web Connection installation, you can simply unzip the Web Connection installation files into an existing Web Connection installation folder.

To ensure all the latest components are registered you should also run `Setup.exe` which re-installs the samples and configures things like the Visual Studio addin and other system settings. This may not always be necessary, but is a good idea to ensure all the right dependencies get updated in your Web Connection install folder.

Consider this the base install and you should not change anything (other than perhaps the samples if you play around with them) as these files will always be overwritten in any update/upgrade.

> **NOTE:** If you changed any of the sample files in the installation folder, these changes will be overwritten. Hopefully you don't make changes to anything in the installation folder, but only in your project folders. We **highly recommend** you always create new projects in separate folders as set up by the [New Project Wizard](VFPS://Topic/_S7R0OXD9T).

### Upgrading from the Shareware Version 
To upgrade from the shareware version you can simply install Web Connection either to a new directory or on top of your shareware installation. If you install on top of an existing installation simply unzip the distribution files into the original directory. 

#### Delete wconnect.app
When upgrading from the shareware version it's important that you **delete wconnect.app**, which holds most of the Web Connection class libraries for the shareware version. In the release version those libraries are replaced with the actual source code classes in the `.\classes` folder.

To delete:

```foxpro
DELETE FILE wconnect.app
```

Other than that upgrading from the shareware version is the same as upgrading from any other version.

Also make sure you have the the `wconnect.h` file from the full version in place.

### Recompile your classes!
If you are updating an existing install or are not installing under VFP 9.0 you should first recompile all of the Web Connection classes to ensure you get the latest code and properly compiled code for your version of FoxPro.  

To do so, start Web Connection in its install directory and do:

```foxpro
DO CompileWC()     && in \tools folder
```

alternately you can manually do this as well:

```foxpro
COMPILE *.prg
COMPILE <wconnectInstallFolder>\CLASSES\*.prg
COMPILE <wconnectInstallFolder>\CLASSLIB CLASSES\*.vcx
```

### Update DLL Dependencies in your Projects
You will also want to update all of the Web Connection dependent DLLs in any of your projects created external to the `wconnect` install folder. 

This includes these base files that should **always** be updated:

* wwipstuff.dll
* wwdotnetbridge.dll
* newtonsoft.json.dll

And these optional specific feature files:

* Renci.SshNet.dll  (SFTP support)
* Markdig.dll       (Markdown parsing)

Basically you can copy **all of the DLLs** out of the Web Connection folder into your project folder as these files are needed when you deploy your application.

You'll also want to update these same files **on your deployed servers** when you update the application there.

### Update Web Resources
In addition to your program files, you may (or may not) want to upgrade your server resources:

* Web Connection Server Modules
* Admin Page
* Required Scripts

Optionally you can also update the stock Web Connection templates from:

* templates/ProjectTemplate/Web/scripts
* templates/ProjectTemplate/Web/css
* templates/ProjectTemplate/Web/lib
* views

For the `./views` folder you'll want to carefully compare old files vs new files **if you overwrote** the existing templates. Specifically `_login.wcs` and `_layoutPage.wcs`.

> #### @icon-warning Overwriting Changes
> Take special care when updating any of your templates if you made changes you might not want to just copy these templates outright over the old files. At the very least it's a good idea to run a tool like <a href="https://www.scootersoftware.com/" target="top">BeyondCompare</a> to compare folders and files and compare changes. While templates often have only minor changes you've made you'll want to see those changes and merge them into the new templates.

#### Update your Web Connection Web Binaries
When you update you'll want to replace the Web Connection handlers appropriate for your project:

* templates\scripts\webconnectionmodule.dll  (.NET Handler)
* templates\scripts\wc.dll  (ISAPI Handler)

You need to update these in each of your projects and also your deployed sites.

#### Update the Web Connection Web Server
If you are using the local, .NET Core, Console application based [Web Connection Web Server](VFPS://Topic/_5LW0YSXQ9) in your project, you should also update that installation. You do so by replacing the `\WebConnectionWebServer` folder in your project, with the `\WebConnectionWebServer` folder from the Web Connection application install folder.

> If you are upgrading from a version prior than 7.26 you also may need to install .NET Core 6, prior versions use .NET Core 5. [More info](VFPS://Topic/_5LW0YSXQ9) in the server information topic.