Web Connection servers can run as EXE based, out of process COM servers or as standalone EXEs. COM servers are more flexible than file servers as the Web Connection connectors can control the lifetime of servers by:

* Explicitly loading servers when the Web Server starts
* Restarting instances if they crash
* Shutting down all instances
* Pausing instances or loading a single instance (for maintenance)
* Live updates of Server Binaries without shutting the Application Pool down
* Provide running totals and runtime information on the Admin panel

For production installations **we highly recommend you run using COM servers**.

* [When to use COM Mode: Production Sites](VFPS://Topic/_4KG0WQR6C)

COM and File Mode are operationally almost entirely interchangeable, so you can easily switch between the two once you've compiled and registered your application. However COM requires some additional setup and configuration for COM registration and potentially DCOMCnfg configuration for non-Admin and non-SYSTEM accounts. 

**You typically use file mode for development and COM mode for production.**

To run in COM Mode:

* Compile your Project (creates a COM EXE)
* Register the COM Component 
* Make sure any dependent DLLS are copied to `deploy` folder
* Test your COM Server from VFP or Powershell to ensure it works
* Allow for live updates of server binaries without shutting down the Web Site
* Switch the Web Application into COM Mode

### Compile your Project
The first step to a COM server is to compile your project into an EXE. Web Connection servers are set up as COM Servers in the declaration and when compiled automatically are available as COM object:

```foxpro
DEFINE CLASS wcDemoServer AS WWC_SERVER OLEPUBLIC
```

> #### COM Compilation requires Administrator Rights
> Because COM Registration requires writing to the registry at the `HKEY_LOCAL_MACHINE` key project compilation requires that you are running as an administrator.
> 
> During development it might sometimes be useful to comment out the `OLEPUBLIC` on the server to avoid having to run as Adminstrator - just remember to add it back in when you're ready to deploy your application. There's an optional `#DEFINE NO_OLEPUBLIC .F.`  at the top of any generated `YourAppMain.prg` file that you can use to switch easily between modes.

To compile your FoxPro application, chose the **Win32 executable/COM Server (exe)**:

![](///images/misc/CompileToCom.png)

or use

```FOXPRO
BUILD EXE wcDemo from wcDemo RECOMPILE

* Alternately you can use `build.prg` in your project folder
DO build.prg
```

Make sure the COM server is compiled as a **Single Use** COM Server which is the default for an EXE server.

![](///images/misc/MiscSingleUseComServer.png)

**It's important you do not use Multi Use** - we need to have a single instance that gets loaded. Multi-uses re-uses a single instance for multiple COM instantiations which is compatible with the multi-threaded Web Server environment.

### Automatic Project Configuration
When you work in the **FoxPro development environment** your COM server is **automatically registered** when you compile your project as long as you run as an Administrator (required for the server to be registered in the registry).

On a **deployed server**, the easiest way to register your COM server is as part of your application's **Server Configuration Script** generated with your project, which includes COM server registration.

From the FoxPro IDE (assuming your EXE exists and you're running as Admin):

```foxpro
do yourProject_serverconfig.prg
```

or running your deployed EXE:

```
yourProject.exe CONFIG
```

Make sure you have checked and tested this setup before running it on a production server.

#### Manual COM Configuration
If you want to do COM configuration manually for the first installation you need to:

* Compile the project into an EXE
* Copy the EXE to the server (could be part of an install)
* Copy Web Connection dependent DLLs to the server
* Register the COM object (`myapp.exe /regserver`)
* Test the Server
* Optionally [Configure the COM object for DCOM permissions](VFPS://Topic/_4KG0Z4G3P)

We already talked about compiling and registering the server above so we won't repeat it here.


##### Register your COM Server
In order to use a COM object on the server it has to be registered on the server in the registry. To do this you need to use the FoxPro COM configuration `/regserver` command on your EXE:

```ps
MyApp.exe /regserver
```

The  auto `CONFIG` operation will run this command automatically during setup, but if you manually configure you **have to make sure you register the server explicitly**!

If the server is not registered you're going to see an error of:

> Invalid COM Server ProgId: MyApp.MyAppServer

If you see this it means one of two things:

* The server isn't registered
* Your Web Application doesn't have permissions to access the registry

For the former, see the following section **Test your COM server** via Powershell and make sure it works outside of IIS.

The latter issue is often caused by not setting the Application Pool's Identity to an account that has the proper permissions to access the registry. Specifically if the default identity of `ApplicationPool Identity` account is left, that account has no rights to access the registry. Choose `SYSTEM` or `NETWORK SERVICE` instead or a local or Domain account that has appropriate rights.

> #### @icon-info-circle DCOM Configuration is required for Non-SYSTEM and Non-Admin Accounts
> If you are planning on using a custom account or `NETWORK SERVICE` you will need to explicitly configure either the machine or the individual server with DCOMCNFG. `SYSTEM` or any local machine Administrator accounts do not require custom configuration, but **any other accounts do**. 
>  
> For more info on how to configure DCOM Configuration see the [DCOM Configuration topic](VFPS://Topic/_4KG0Z4G3P).

### Copy the Web Connection Dependent DLLs
Web Connection requires a few of support DLLs in order to run and you need to ensure that those DLLs are accessible to your COM server. When the COM server launches its root path becomes the path where the EXE lives and by default the Web Connection DLLs don't exist there and the server won't look in the Web Connection install folder for them because it doesn't know where that folder lives. On a live server too you need to ensure that those files exist.

At minimum you'll need the following dll dependencies in your `deploy` folder:

* wwipstuff.dll
* wwdotnetbridge.dll
* newtonsoft.json.dll

There are additional dll files for specialty tasks like Markdown parsing and FTP support. Basically all the DLLs out of the install directory should be copied.

You can use the build script in the project root folder `build.ps1` to copy the required files into the `.\Dist` folder, or - maybe more easily for local testing you can simply do from a Command Prompt in the `Deploy` folder of your project:

```dos
copy c:\wconnect\*.dll .\
```

> #### @icon-warning Update your DLLs with Web Connection Versions
> When Web Connection is updated the DLLs often are updated as well. While there rarely are breaking changes, it's always a good idea to keep your Web Connection version in sync between the FoxPro source version and the binaries that ship with each new release. The westwind DLLs are all versioned to match the latest Web Connection version.
> 

### Testing your COM Server
Ok so now your server should be registered and ready to use.

You can now test it easily either in FoxPro or using a simple PowerShell script  you can run from Windows.

If you have the FoxPro IDE installed try the following from your FoxPro Command Window:

```foxpro
loServer=CREATEOBJECT("yourProject.YourProjectServer")
? loServer.ProcessHit("query_string=wwMaint~FastHit")
```

If you don't have the FoxPro IDE installed on a production machine you can create test using **Powershell** which should print some HTML to the Console:

```ps
# instantiate the server
$server =  new-object -comObject 'yourProject.yourProjectServer'

# prints a simple HTML page HTTP Response (ie. it works!)
$server.ProcessHit("query_string=wwMaint~FastHit")

# or point at a valid physical page url
# $server.ProcessHit("&PHYSICAL_PATH=c:\webconnection\web\wconnect\TestPage.wwd")

# release the server (optional)
[System.Runtime.Interopservices.Marshal]::ReleaseComObject($server) | Out-Null
```

### Enabling COM Operation on your Web Site
Once the COM server is configured you need to enable loading of the COM server. You do this via the server configuration in `web.config` for .NET or `WebConnectionServerSetup.xml` for the Web Connection .NET Core Web Server.

You can do this on the Web Connection Module Administration page.

Go to:

http://localhost/wconnect/Administration.wc

where `/wconnect` or the root site is your virtual directory of your application. Then click **Web Connection Module Adminstration** and scroll down to the **Server Administration -> Messaging Mechanism** and switch it to COM. It should look like this:

![](///images/misc/SwitchToComMode.png)

The link toggles between **File** and **COM** modes. 

This switch corresponds to the `MessagingMechanism` setting in `web.config` in .NET mode, or `Mechanism` in ISAPI mode.


Once you've switched to COM, servers either start up automatically when you hit the server with a request, or you can explicitly load the servers using the **Load Servers** link on the same page. Once loaded you should see the loaded servers in the server list at the top of the page.

Here's more info on the configuration settings in the configuration files.

#### .NET Handler (web.config)
The .NET handler configures COM as part of the web.config settings in the `webConnectionConfiguration>` section:

```xml
<webConnectionConfiguration>
    <add key="MessagingMechanism" value="Com" />
    <add key="ComServerProgId" value="wcDemo.wcDemoServer" />
    <add key="ServerCount" value="2" />
    <add key="ComServerLoadingMode" value="LoadBased" />
    <add key="ComServerPerServerLoadTimeoutMs" value="2000" />
</webConnectionConfiguration>
```
Most importantly make sure the `MessagingMechanism` is set to `Com` which enables COM operation. This value can also be toggled from the .NET Handler Status page.

Specify the `ComServerProgId` and `ServerCount` to indicate which server to load and how many instances to fire up (up 16). The `ComServerLoadingMode` determines how servers are loaded up either in `LoadBased` which loads servers based on the first COM server available or `RoundRobin` which always rotates servers through the entire list of servers.

The `ComServerPerServerLoadTimeoutMs` flag determines how long Web Connection waits for servers to load. This value is used to determine how long to wait for all servers to load before considering them timed out based on calculation of `ServerCount` x `ComServerPerServerTimeoutMs`.

### Web Connection Web Server
The same setting names apply for the Web Connection Web Server but using the raw XML format:

```xml
<MessagingMechanism>Com</MessagingMechanism>
<ServerCount>2</ServerCount>
<ComServerProgId>wcDemo.wcDemoServer</ComServerProgId>
<ComServerLoadingMode>LoadBased</ComServerLoadingMode>
<ComServerPerServerLoadTimeoutMs>2000</ComServerPerServerLoadTimeoutMs>
```