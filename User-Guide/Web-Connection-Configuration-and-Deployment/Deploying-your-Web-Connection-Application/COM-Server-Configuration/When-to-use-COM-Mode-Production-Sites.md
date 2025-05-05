Web Connection supports both File based and COM operation. File based is meant for development while COM based is meant for deployed applications, but it's also possible to run file based in production.

### COM Advantages

* **COM Servers start automatically**  
COM Servers are automatically launched on the first hit to the server. They also shut down if the Web application shuts down. They do not need to run as a service or require any other auto-start mechanism.

* **Server Crash Recovery**  
Reality: FoxPro servers crash occasionally, try as you might. Using COM Web Connection can detect failures immediately and restart crashed COM servers automatically. This is not possible in file mode.

* **Better Remote Management**  
COM servers can be administered entirely over the Web. You can easily start and stop instances and change the count of instances running. You can get basic server execution stats. You can also remotely update your Web Connection Server binary without shutting down the site, simply by uploading a new binary and hot-swapping the new binary on the fly.

* **Improved Performance in high volume environments**  
COM operation provides better performance under load as it eliminates the message files that are used for file based messaging. Under low load scenarios the difference is minimal but under heavy load COM can <a href="http://west-wind.com/weblog/posts/1475.aspx" target="top">significantly improve throughput</a>.

### COM Disadvantages

* **Requires additional Configuration**  
COM Servers [require extra configuration initially](dm-topic://_s901dvuwg). COM servers have to be COM registered (`/regserver`) on the server, and depending on the security context you're running under (non-admin, non-SYSTEM) you may have to [configure COM servers or the machine with DCOMCnfg](VFPS://Topic/_4KG0Z4G3P).

* **Fixing Server Startup Problems**  
If you have problems with server startup, it's more difficult to find and fix startup errors because typically COM servers run invisibly. Effectively you have to use log tracing to debug and fix errors. Here's more info in [Dealing with COM Server Load Errors](VFPS://Topic/_4BZ0UBTI4).
  
  The main source for problems tends to be due to changes in permissions, user profile usage or using one of the very few FoxPro features that don't work in COM.

### Advantages of use File Based Servers

* **No COM registration required**  
File servers are standalone EXEs so they can be launched from Explorer or the Terminal. Given that COM and File based servers use **the same EXE** however, you can switch back and forth easily.

* **File mode can run interactively**  
It's possible to run the server interactively and from within the  Visual FoxPro which means it's easier to debug. When run from Explorer or the Terminal the servers are visible so any errors are visible in the server UI and you can if necessary run the application in the IDE and use the FoxPro debugger, even on a production box (assuming VFP IDE is installed). Note that this can be helpful even for COM Debugging if the error is a code error vs. an environment error.

* **Server can run on a machine separate from Web Server**   
You can run a file based server on separate machine from the Web Server by pointing the semaphore temp file folder to a network location. This makes it possible to have multiple machines serve data provided by the Web server. It also makes it possible to use a non-Windows machine for the Web server.
  
  Using the Web Connection .NET Core Web Server it's also possible to **host the Web Connector portion of Web Connection on a non-Windows machine**. Temp files can be placed in a central location accessed by FoxPro servers on separate machines.  

  *FoxPro servers still have to run Windows or a Windows emulation while the Web Connection Web Connector does not.*  

  *As cool as it sounds to have multiple computers with many instances accessing a single Web site serving requests and providing file access for multiple machines, it's difficult to manage these distributed instances and handle their lifetime for startup/shutdown and crashes.*

### COM and File Mode: No code changes required
COM operation is supported transparently by Web Connection and you can very easily switch back and forth between COM and File modes. Other than potential security and user profile related issues (if your COM server launches under different credentials) and very few operational differences in COM servers in FoxPro, **file and COM server applications behave exactly the same**. 

The fact that you can easily switch back and forth is very useful. If you run into errors with a COM based setup, the first thing you should do is switch to File Based and see if the server runs that way. If it doesn't, then you likely have a basic code issue that needs to be fixed that is unrelated to COM.

If the problem is truly COM specific then you likely have an environment issue that is due to the difference in the user account or user profile.

Most COM specific errors have to do with:

* **Potentially wrong Security Permissions**  
Make sure the IIS Application Pool has its identity properly configured and if you're not running an Admin or SYSTEM account make sure that the [appropriate DCOMCnfg permissions are set](VFPS://Topic/_4KG0Z4G3P). When in doubt with permissions start with an Admin or the SYSTEM account impersonation for the IIS Application Pool and ensure that that works, then go back and adjust the appropriate permissions.

* **COM Specfic Features and Environment**  
FoxPro has a few features that don't work in COM. UI operations famously don't work in COM and some interactive UI operations can throw errors. Also the environment may be different if you are accessing User specific resources like the User registry or user application folders. You can fix the latter by setting up the IIS Application Pool to explicitly provide a user profile (by default it doesn't).

* **Slow Startup**  
Make sure your servers start up as quickly as possible. **Do not put any non-essential application code in the `OnInit()`** of your server class, as you want the server instance to be created as quickly as possible and with the lowest possibility of producing an error. Any server and environment configuration code should go into the server's `OnLoad()` code.  `OnLoad()` execution is delayed until the first request is made to the server, and thus is somewhat separate from the initial server loading process.

> #### @icon-warning Server Startup Code should be set in wwServer::OnLoad()
> Make sure you **do not have server startup code in the `YouAppMain.prg` startup code snippet** at the top of the PRG. The PRG startup code **is never fired in COM mode**. This is a common mistake that comes up!
>
> Servers should make any server specific configuration changes in `YourServer.OnLoad()` in your server class. 

### Using File Mode in Production
Generally COM is recommended for production applications because it is faster and provides better administration.

But there are situations where File base operation can be used.

#### Debugging
If you have a persistent problem in one of your application, running in file mode or running through the Visual FoxPro IDE even with source code can provide better insight into the application as you can stop operation and potentially attach a debugger.

You can also run interactively on the desktop which can make it easier to stop code and display debug messages or `ASSERT` messages.

#### Scale over many Machines
COM is a local machine protocol. Although COM has support for Remote Activation of components, that functionality is very difficult to set up correctly and more importantly very buggy and prone to random unloads and crashes. It's  **not recommended** to use Remote DCOM servers.

File mode however allows you use multiple machine to process requests against a single Web Connection server by **sharing messaging file in a shared temp messaging folder**. Multiple machines can run multiple Web Connection instances each looking at the same network path where Web Connection Temp Files are picked up. 

File mode also potentially allows you to run the Web Connection .NET Core Web Server on a non-Windows machine and make files available to Web Connection FoxPro instances running on separate Windows server machines.

This can allow many machines with many instances to share a single Web site for request processing.

Although this approach is quite possible and has been successfully used, this mechanism also has limitations due to Windows' problem with large numbers of files in a single folder that Web Connection uses for messaging. However it can work well for applications that have many long running requests that may tie up server instances for lengthy periods of time.

Ultimately, the preferred approach for scalability is to use proper load balancing with multiple identically configured machines running their own set of Web Connection instances behind a hardware or software load balancer that fires requests at these multiple machines.