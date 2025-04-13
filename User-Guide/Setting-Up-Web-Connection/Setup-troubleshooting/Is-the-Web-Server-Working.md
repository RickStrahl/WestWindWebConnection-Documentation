The first thing to check is to see whether the Web Server and Web Connection are running.

>The following assumes you're working with the Web Connection sample application at http://localhost/wconnect, but these steps can apply to your own applications in your own virtual directory and/or server.

### 1. Is your Web Server working?  
**Filebased/COM**  
First of all make sure that your Web server is running and that you can bring up a static page in your Web application.

Go to:

**http://yourserver.com/virtual/default.htm**  

and make sure that this page comes up. For the Web Connection demo the page to check is:

<a href="http://localhost/wconnect/default.htm" target="top">http://localhost/wconnect/default.htm</a>

If this page doesn't come up your Web server is not functioning correctly. Check the [IIS setup and configuration page first](VFPS://Topic/_22F0XKBMQ).

### 2. Is the Web Connection .NET or ISAPI Module working?
**Filebased/COM**  
Next, check to see whether the Web Connection module is actually working. You can do this by hitting the **Web Connection Module Administration** link from the Administration page. 

![](IMAGES/misc/ModuleStatusAdminPaths.png)

The URL for this operation is:

**http://localhost/wconnect/admin/wc.wc?_maintain~ShowStatus**  

If the page comes up, the Web Connection module is working. Note that the .NET module and ISAPI pages look slightly different, but if it comes up the Web Connection server component is working.

> @icon-lock Login Security
>Most likely you will have to provide credentials in order to access this page - use a valid server username and password to gain access to the admin and related links. Note that if you are on the actual server you might have to configure [Loopback Protection on Windows Server](VFPS://Topic/_4GI0QL5JB).

### Is the Web Connection FoxPro Server running?
**Filebased**  
In order for Web Connection to serve requests, make sure that the FoxPro server is actually running. In File mode this means you have to ensure that the server is started either via the EXE itself or from the FoxPro IDE by running the application.

If unsure that your EXE is running check Task Manager for your EXE. If it isn't, start it manually from Explorer or the Command Line.

> #### @icon-info-circle AutoStart File Servers
> You can use the `AutoStartServers` and `ServerCount` (.NET Module web.config) or the `FileStartInstances` (ISAPI wc.ini)  configuration options to automatically launch Web Connection servers when IIS starts ([more info](VFPS://Topic/_22f0xkbmq#yourproject_serverconfig.prg))


### Does the Web Connection COM Server come up?
**COM**  
If you are running in COM mode you should hit one of the Admin links on the Admin page that returns the Web Connection Server status. These pages hit the Web Connection server and return various pieces of information from the Web Connection server.

**http://yourserver.com/virtualIfAny/wc.wc?wwmaint~ShowStatus**  

For the sample application:  
**http://localhost/wconnect/admin/wc.wc?wwmaint~ShowStatus**  

If this request comes up your COM server is loading and working.

### COM Server doesn't come up
If the COM server does not come up check task manager to see if the server EXE is listed. If it is the server might be coming up in COM but crashing during initialization. If that's the case, check the startup log


If your COM server doesn't come up you need to look at the COM error messag that is likely to display which might give more information.