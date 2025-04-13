> ### @icon-warning Obsolete: Use the YourApp_ServerConfig.prg Scripts
> Please don't use the configuration console for configuration but rather use the new project wizard generated `YourApp_ServerConfig.prg` generated into your project. See [The Generated Server Configuration Script](VFPS://Topic/_4LS0MOTZS)

This wizard is responsible for configuring Web Connection applications on a new server. Typically you'll run this Wizard after you've created an application locally and now need to move it to a server. 

This Wizard allows you to set up or configure virtual or root Web site folders, configure scriptmaps for that folder, configure and synch the Web Connection configuration files and register COM servers. In short it can handle all of the typical IIS and COM configuration tasks required for a Web Connection application.

<div class="notebox">**Note**  
The configuration Wizard will not copy any files from your client to the server - it is purely a server configuration utility that **must be physically run on the machine to be configured**. This means you need physical access to the machine in question or some mechanism like pcAnywhere to run it.</div>

This Wizard is selective and allows partial operations to be performed. For example, you can only create a scriptmap or only create a virtual directory, or you can do both. In other words, this Wizard is very versatile and can be used for many common Web server and registration tasks, which is especially useful when installing Web application on a live server or passing on steps to perform by an ISP.

### Requirements
This wizard unlike the others does not generate any code nor configures any Web Connection specific features. This Wizard is purposefully generic so you can run it on a server. You will need the following in order to have full functionality:

* The Wizard must run out of the Application startup directory!
* CONSOLE.EXE must be present.
* The Web site to configure your app on must be already created
* A .\scripts subdirectory must exist below the root and this directory must contain wc.dll/ini (as per Web Connection installation). These files are required in order to be copied to the Web directories.
* DCOMPermissions.exe from the .\tools directory must be available to use the DCOM configuration settings.

---

Goto [Step 1](vfps://Topic/Step%201%20-%20Create%20virtual%20directory)