As of version 5.60 Web Connection supports registrationless COM deployment. Registrationless COM deployment runs Web Connection applications as MTDLL COM servers hosted inside of IIS and uses the Web Connection .NET Managed Handler to interact with IIS.

Registrationless COM provides these benefits:

* Low impact server installations
* Requires no Visual FoxPro Runtime installations (you provide the runtimes)
* Requires no COM registration of a Web Connection Server
* xCopy deployment of site layout
* Ability to host with some low cost discount ISPs

This topic describes all the steps required to get registrationless COM to work including creating all the files required manually. New projects automatically generate the deployment folder and template manifest files so a few of the steps mentioned in this detailed outline are already set up for you. The goal of this topic is to show all aspects of the process from scratch.

This process works by creating a webconnection.deploy folder that contains all executable, data files, the FoxPro runtimes and a couple of manifest files that tell Web Connection/IIS how to load your MTDLL COM component without being registered in the registry.

### Requirements
The requirements for registrationless COM to work are:

* .NET 2.0 or later enabled in IIS and the virtual directory or root site
* IIS must be allowed to run in Full Trust <trust level="Full" /> in web.config
* IIS 7 or later
* Your Web Connection application must run as an MTDLL server

There are a few limitations in FoxPro in regards to running MTDLLs:

* Report writer access is not available (DLL servers can't run reports)
* No UI support for Web Connection Server
* Registrationless loads don't work in Visual Studio Web Server (Cassini)
* Registrationless loads do work in the Web Connection Web server (based on Cassini)

Please realize that the process for registrationless COM requires some manual configuration and that your server be recompiled to use an MTDLL server. You also need to set up to run using the Web Connection .NET Managed Handler.  However, once you get your site running locally using this configuration, getting the site deployed to a live server is simply a matter of uploading the files.

This topic describes the process in full.

### Building your Application
The first step is to build your application and make sure it all works as expected. As always we recommend NOT to develop your application in COM mode - use file based mode so you can easily debug your application, step through code and interact with your code in a natural way for development.

### Compile your Application into an MTDLL
Once your app is a stable state, compile the application into an MTDLL component. Use

```foxpro
BUILD MTDLL MyApplication from MyApplication RECOMPILE
```

<div class="notebox">**Build an EXE first for Testing**  
It might actually be easier to first compile to an EXE and test the application that way first, then once that all works compile into an MTDLL component.</div>

Make sure your application works with the MTDLL before attempting any registrationless installation!

### Copying files
Create a new folder in your Web application's site and name it:

**webconnection.deploy**  

then copy all files that your application needs to run:

* MyApplication.dll (the compiled MTDLL)
* MyApplication.ini
* config.fpw (IMPORTANT: SAFETY=OFF, RESOURCE=OFF)
* wconnect.h  (required for dynamic WCF Page compilation)
* VFP9T.dll
* VFP9rEnu.dll  (or culture specific version for your locale)
* msvcr71.dll
* wwIPStuff.dll
* wwDotNetBridge.dll1
* zlib1.dll
* Any other files your application uses (Dbf files, INI files)

Make sure you include all local dependencies of your application here either in this folder or subfolders and that your application sets paths to these folders when it starts up (in yourAppServer.OnLoad()).

### Switch your Application to use the Web Connection Handler in web.config
Next it's vital that you use the Web Connection Handler to run your Web application because the custom ActivationContext invokation is handled through some .NET code. 

There are two things that need to be set up:

* Your script maps need to point to the Handler
* The ActivationContextModule needs to be activated

**IIS 7 and later (Windows 7, Vista, 2008)**  
For IIS 7 and later in integrated mode your need to add the following (some of these may already be set or commented in your web.config file):

```xml
<system.webServer>
  <validation validateIntegratedModeConfiguration="false" />
  <handlers>
    <!-- Web Connection Handler in IIS 7 in Integrated Mode -->
    <add name="*.sbsd_wconnect" path="*.sbsd" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" preCondition="integratedMode,runtimeVersionv2.0" />
    <add name="*.wc_wconnect" path="*.wc" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" preCondition="integratedMode,runtimeVersionv2.0" />
    <add name="*.wcsx_wconnect" path="*.wcsx" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" preCondition="integratedMode,runtimeVersionv2.0" />
    <add name="*.wcs_wconnect" path="*.wcs" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" preCondition="integratedMode,runtimeVersionv2.0" />
    <add name="*.wwsoap_wconnect" path="*.wwsoap" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" preCondition="integratedMode,runtimeVersionv2.0" />
    <!-- end IIS 7 in Integrated Mode -->
  </handlers>

  <modules>
    <add name="WebConnection_ContextManifestModule"
         type="Westwind.WebConnection.ActivationContextModule,WebConnectionModule" />
  </modules>
```

You can add/remove any of the scriptmaps as needed to this list. These maps are portable and are stored in web.config - they are xcopy deployable so no further configuration is required when you move them to a live site!

The module reference is a seperate entry in the <modules> section

### Create your Virtual Directory
If you're running on an ISP hosted site you don't need to follow the next steps since the ISP will provide your site or virtual directory for you.

If you're copying to your own server, create a virtual directory (or Web Site) for the Web folder. Assign an Application Pool to it and make sure the Application Pool Identity has rights to read and write in this folder and has access to any other resources you might need to run the application (SQL Connections, files on the system etc.). 

For more information on setting up a virtual directory and application pool see the Server Configuration Topics for [Windows 7,2008](vfps://Topic/_22F0XKBMQ) and [XP, 2003](vfps://Topic/_0V81D7Y5Q) here.

### Creating Manifest Files
In order for registrationless COM to work you need to create a couple of manifest files in your Web folder. 

**WebConnection.manifest (site root folder)**  
This is the application manifest file which routes the COM loading to a specific folder. It looks like this:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<assembly xmlns="urn:schemas-microsoft-com:asm.v1"
          manifestVersion="1.0">

  <assemblyIdentity name="web"
                    version="8.0.0.0"
                    type="win32"
                    processorArchitecture="x86" />

  <dependency>
    <dependentAssembly>
      <assemblyIdentity name="webconnection.deploy"
                        version="1.0.0.0"
                        type="win32"
                        />

    </dependentAssembly>
    
  </dependency>
</assembly>
```

This routing now forces the Web application to look for webconnection.deploy.manifest in the current folder or webconnection.deploy folder. We'll use the latter so we can isolate our executable code out of the root folder of the Web application.

<small>(as of Web Connection 5.60 this manifest is auto-created for new projects)</small>

**webconnection.deploy.manfiest (~\webconnection.deploy folder)**  
Create this file in the webapplication.deploy folder where you installed the DLL and your code. This file needs to be configured to include your DLL server's:

* CLSID
* ProgId
* TypeLibId

Here's what the file looks like for one of my sample COM objects:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0">

<assemblyIdentity name="webconnection.deploy"
   type="win32"
   version="1.0.0.0" />

<file name="myapplication.dll">
    <comClass description="MyApplication Server"
         clsid="{EF82B819-7963-4C36-9443-3978CD94F57C}"
         progid="MyApplication.MyApplicationServer"
         tlbid="{29763137-9FA2-4A34-8837-68A6BF6AC53A}"         
         threadingModel="apartment" />         
</file>

</assembly>
```

You can find the ClsId, ProgId and TlbId in the *myapplication.vbr* file that is generated in the same folder as the DLL when you compile your FoxPro COM object. Simply copy the values from the VBR file into the appropriate slots in the above manifest. See lines in bold below to copy from in myapplication.vbr:

```text
VB5SERVERINFO
VERSION=1.0.0

HKEY_CLASSES_ROOT\sidebysidedeploy.SidebysidedeployServer = myapplication.MyApplicationServer
HKEY_CLASSES_ROOT\sidebysidedeploy.SidebysidedeployServer\NotInsertable
**HKEY_CLASSES_ROOT\sidebysidedeploy.SidebysidedeployServer\CLSID = {EF82B819-7963-4C36-9443-3978CD94F57C}**  
HKEY_CLASSES_ROOT\CLSID\{EF82B819-7963-4C36-9443-3978CD94F57C} = myapplication.MyApplicationServer
**HKEY_CLASSES_ROOT\CLSID\{EF82B819-7963-4C36-9443-3978CD94F57C}\ProgId = myapplication.MyApplicationServer**  
HKEY_CLASSES_ROOT\CLSID\{EF82B819-7963-4C36-9443-3978CD94F57C}\VersionIndependentProgId = sidebysidedeploy.SidebysidedeployServer
HKEY_CLASSES_ROOT\CLSID\{EF82B819-7963-4C36-9443-3978CD94F57C}\InProcServer32 = myapplication.dll
HKEY_CLASSES_ROOT\CLSID\{EF82B819-7963-4C36-9443-3978CD94F57C}\InProcServer32\"ThreadingModel" = Apartment
**HKEY_CLASSES_ROOT\CLSID\{EF82B819-7963-4C36-9443-3978CD94F57C}\TypeLib = {29763137-9FA2-4A34-8837-68A6BF6AC53A}**  
HKEY_CLASSES_ROOT\CLSID\{EF82B819-7963-4C36-9443-3978CD94F57C}\Version = 1.0
HKEY_CLASSES_ROOT\CLSID\{EF82B819-7963-4C36-9443-3978CD94F57C}\Foxruntime = VFP9T.DLL

... * ** more content omitted for brevity
```

### Enabling RegistrationLess COM Loading in web.config
The final step is to enable registrationless COM loading in Web Connection. To do this you can set the UseCustomManifestLoading flag in the <WebConnectionConfiguration> section of the web.config:

```xml
<webConnectionConfiguration>
    <add key="UseCustomManifestLoading" value="True" />
  </webConnectionConfiguration>
```

This enables the ActivationContextLoading features in Web Connection to look for the webconnection.manifest file and read the registration info from it.

### Ready to test your application
Your application is now ready for running registrationless. Make sure you have manually unregistered your DLL server before testing otherwise you may end up using fully registered loading instead of registrationless loading.

To unregister your COM server use:

**RegSvr32.exe -u MyApplication.dll**  

Once all this is done you should now be able to load your COM object even though it is not registered via COM. Note that if you previously loaded the COM object you will have to restart IIS (or the application pool) with:

**IISReset.exe**  

from the Windows Command prompt. Reload your page and see if the COM object still works even though it is now no longer registered.

Once you've tested the app locally or on a staging server you should be able to xCopy deploy it to a live server by simply copying all files. Scriptmaps, Context Activation, the manifests are application local so it should just work. The only manual step is creating the virtual and setting up the Application Pool.

### Important: ActivationContext Registration Issues
The ActivationContext API that allows for dynamic registrationless COM loading has many small quirks that are important if you are testing your servers:

* **ActivationContext can be loaded exactly once per process lifetime**  
In IIS this means if there's a problem loading a manifest, you won't be able to change the manifest and see the changes until you restart IIS or the ApplicationPool (w3wp.exe).

* **ActivationContext manifests are cached by Windows**  
Once a manifest has been loaded Windows holds on to the settings in it until you reboot or the underlying executables change. This means that if you have a failure in loading, Windows will often continue loading the same faulty manifest file even if you fix it. This makes for tricky debugging. The best way to fix this is to recompile your DLL which makes Windows recognize the file change and reload manifest files for it.

Therefore it's crucially important that you test this installation locally on your development/deployment machine and test to ensure that the registrationless configuration is correct and runs. Only then should you send up your configuration to a live host where you might not have the chance to reconfigure the Web server after a manifest load fails.