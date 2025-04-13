Please read this document if you're installing Web Connection for the first time or doing an update installation. It contains important information that might save you some trouble during installation and especially during updates.

**Requirements:**
* Windows 7-11, Windows Server 2012-2022
* Windows Web Server IIS 7 or later, IIS Express or Web Connection Web Server
* Visual FoxPro 9.0

**Problems?**
* [Post a message on the Support Message Board](https://support.west-wind.com)

> #### @icon-info-circle Side by Side Installs
> It is possible to install multiple versions of Web Connection on a machine, side by side. We recommend you rename an old version (`wconnect_<version>`) then install the new one in it's normal `wconnect` location. Both versions can be used side by side - you just need to make sure that `SET PATH` points at the right install location.

### Extracting the Self Extracting Zip Archive
You can <a href="http://west-wind.com/webconnection/download.aspx" target="top">download the shareware version of Web Connection</a> from a public link, while the full, registered version download link is provided to you via a **private registration email**. The shareware version is named `WebConnection.exe`, the registered version is named `WebConnection-<version>.exe`.

Files are self-executing **7zip archives** and can be **executed as an EXE** which prompts where to unpack the installation files. Note that the EXE is not the installer - **it only unpacks files**. Alternately, the EXE can also be **opened with <a href="http://www.7-zip.org/download.html" target="top">7Zip directly</a>**, which lets you lets you pick out individual files.

By default files are unpacked to `c:\wconnect`.

> #### @icon-globe Install Web Server Prerequisites **before** running Setup
> In order to run Web Connection you need to have one of the following Web Servers installed:
>
> * **[For IIS](VFPS://Topic/_22F0XKBMQ)**  
> Either use the provided `Install-IIS-Features.ps1` Powershell script, or explicitly install IIS using Windows Features (Win11/10/8/7) or Server Manager (Server).
> * **[For IISExpress](VFPS://Topic/_3NJ01RJ5N)**  
> Download IIS Express 10 from the Web and run the installer to install the local, launchable and non-admin IIS Express Server.
> * **[For Web Connection Web Server](VFPS://Topic/_5LW0YSXQ9)**  
The Web Connection Web Server comes with Web Connection and is ready to run, but depends on an installation of .NET Core 8.x or later.

## <span style="color: firebrick">Run Setup.exe</span>
Once files are unpacked, open up Explorer in `c:\wconnect` (or wherever you extracted to) and execute `Setup.exe`. This process installs or updates only the Web Connection Samples and sets up the Web Connection base install as well as configuring Visual Studio and VS Code (if installed). **Setup does not touch any of your own project files**, so it is safe to run even for update installs. 


Once complete the installation automatically launches a Web Browser and the sample Web Connection Server in the Visual FoxPro IDE (using `DO Launch.prg` or `Launch()`).

### Start the Project Manually
Once Setup has completed it will automatically launch the sample application and you can play with it. 

You can always restart the demo with these steps:

* Open the West Wind Web Connection Desktop Shortcut
* Make sure you are in the install folder or else `cd \wconnect` 
* `DO Launch.prg`
* <a href="https://webconnection.west-wind.com/docs/_s8h1ez705.htm" target="top">More info in the documentation</a>

### Create a new Project
To create a new project use the **Web Connection Management Console**:

* Run `DO Console` and pick **Create New Project**
* [More info Creating a new Project](_s8g12b92q.htm)


## First Time or Update Installation
Please run `Setup.exe` from the install directory and just accept all the prompts. There are detailed instructions on what the [Setup program does and how to get started](VFPS://Topic/_S7R0OBMM1). This will install the sample Web folders, set permissions and get the samples ready for you to play and check out Web Connection in operation.

> #### @icon-warning No FoxPro Runtimes?
> If you do not have the VFP runtime files installed you can also run the appropriate Setup.exe file from within Visual FoxPro. Start Visual FoxPro and CD <installDir> then DO SETUP.exe to launch setup from within VFP. Make sure you use **Run As Administrator** as configuration features require full admin access.

### Installing IIS
If you want to [install a full version of IIS](VFPS://Topic/_22F0XKBMQ) on Desktop or Server versions of Windows we've provided a Powershell script to enable all the Web server features required to run Web Connection.

To install IIS on your Windows machine:

Open an **Administrator Powershell Command Prompt** and type:

```powershell
cd \wconnect  
.\Install-IIS-Features.ps1
```

You can also <a href="https://west-wind.com/webconnection/docs/_22f0xkbmq.htm" target="top">install those same features manually</a> using **Add Windows Features** (Desktop) or **Add Server Roles** (Server).

### Updating an existing Installation
If you are updating an existing installation, running `Setup.exe` is optional, but still recommended. You'll want to re-run setup on your existing installation on major updates to insure the latest example files are installed and hooked up. 

If you do it's recommended you choose the same setup directories as in the previous installation. 

> #### @icon-warning Don't change Web Connection Files!
> If you changed any of the sample files or Web Connection classes installed, the **changes will be overwritten**. Hopefully you wrote code in your new project folder which is completely isolated.
>
> Always create new projects to work on, or copy the samples to a new location, never use the samples directly for your own code. If you need to make changes to framework classes **subclass** them in a separate folder or as part of your application, rather than changing the original classes as **they will be overwritten by updates**.

### Upgrading from the Shareware Version 
To upgrade from the shareware version you can simply install Web Connection either to a new directory or on top of your shareware installation. If you install on top of an existing installation simply unzip the distribution files into the original directory. 

##### Delete wconnect.app
When upgrading from the shareware version it's important that you **delete wconnect.app**, which holds most of the Web Connection class libraries for the shareware version. In the release version those libraries are replaced with the actual source code classes in the `.\classes` folder.

To delete:

```foxpro
DELETE FILE wconnect.app
```

##### Recompile your classes!
If you are updating an existing install or are not installing under VFP 9.0 you should first recompile all of the Web Connection classes to ensure you get the latest code and properly compiled compiled code for your version of FoxPro.  

To do so, start Web Connection in its install directory and do:

```foxpro
COMPILE *.prg
COMPILE CLASSES\*.prg
COMPILE CLASSLIB CLASSES\*.vcx
```

### What's installed
The installed folder contains the base Web Connection installation which contains the Console application and sample server and Web sample pages that demonstrate how Web Connection works.

The Console application is the hub for Web Connection's administration features that allow you to create new projects or add functionality to existing projects as well as add new features and functionality. The bulk of the files installed are templates base files that are used to create new projects or new files.

The install also creates a sample Web Connection server and a number of corresponding Web samples which you can run and examine to see how Web Connection works. 

There's a feature sample that shows many of the core features of Web Connection in very simple task based examples. Then there are a host of example applications that demonstrate a number of different approaches to building more complex Web Connection applications. The examples include a Weblog, a Message Board, a AngularJS client side AlbumViewer example.

### If you get stuck
If you run into problems with your installation or getting started there are a few resources available. 

* **<a href="http://west-wind.com/webconnection/docs/" target="top">Online Documentation</a>** (you're looking at it!)
* **<a href="http://support.west-wind.com">West Wind Message Board Support Forum</a>**

### Documentation
The online documentation is extensive and provides a lot of useful information. Most tasks have walk throughs that you can check out to understand a feature or process. The documentation is searchable so you can find what you're looking for quickly using keywords or partial matches in the search box.

* **<a href="http://west-wind.com/webconnection/docs" target="top">Online Documentation</a>**

### West Wind Message Board Support Forum
We have a message board that you can use to post questions and get answers to in a timely manner from the authors or from the community. For any support issues - bugs, comments and suggestions - the message board is the place to go. We answer all questions within a day, and most questions within a much shorter time frame. This is the best place to have a discussion about issues and problems or simply to ask questions if you get stuck.

* **<a href="http://support.west-wind.com">West Wind Message Board Support Forum</a>**

We also offer paid support if you feel you need more complex support or are dealing with company specific or sensitive sensitive issues.

* **<a href="http://west-wind.com/contact/" target="top">Paid support with Rick Strahl</a>**