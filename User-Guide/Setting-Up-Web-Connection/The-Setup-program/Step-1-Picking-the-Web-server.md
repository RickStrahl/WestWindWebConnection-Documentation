The first step in configuring Web Connection is to pick the Web server to run on. This choice is very important as it will determine how to configure the server as well copying the appropriate files. The setup expects the Web Server and support environment to be already configured. When you make a selection it will attempt to check for the installed server and if it doesn't find it send you to the documentation for the installation of that particular server.

Once you're sure your Web Server is installed you can run the Setup program.

* Run `Setup.exe`  (from Explorer)

This brings up three step Wizard:

![](/images/ManagementConsole/setup1.png)


### Web Server Choices
The first page lets you select your Web server to use:

* **[Internet Information Server (IIS) with .NET Handler](https://webconnection.west-wind.com/docs/_22f0xkbmq.htm)**  
Use IIS if you have access to admin rights and want to run your application without explicitly starting a Web server as IIS is *always-on*. Note you have to make sure [IIS is installed](https://webconnection.west-wind.com/docs/_22f0xkbmq.htm) prior to running this Setup. This is the most complex installation type and most prone to configuration issues, but running IIS locally gives you the exact same Web Server you are likely to run in production.

* **[IIS Express with .NET Handler](https://webconnection.west-wind.com/docs/_3nj01rj5n.htm)**  
IIS Express is a small, self-contained, installable and manually started version of IIS that can be run as an application on Windows. It doesn't require Admin rights to install or run, and it provides high feature compatibility with full IIS. Using IIS Express requires that the server is **started explicitly**, although Web Connection's `Launch()` command automates that process for you. [IIS Express has to be explicitly installed](https://webconnection.west-wind.com/docs/_3nj01rj5n.htm) before it can be used.

* **[Web Connection Web Server on .NET Core](https://webconnection.west-wind.com/docs/_5lw0ysxq9.htm)**  
The Web Connection Web Server is a self-contained, .NET Core based Web server that can be distributed along-side your application. The server runs as a Console application and can be distributed with your application, which makes it possible to run your Web application on any machine without installing any server software (although you do need a .NET Core install). It can also run inside of IIS similar to the .NET Handler so it covers both the standalone operation as well as IIS operation from a single distributed binary shipped with your application.

  The server is optimized for Web Connection request handling and has a single configuration model independent of IIS or another server. [The server has a dependency on the .NET Core Runtime](https://webconnection.west-wind.com/docs/_5lw0ysxq9.htm) that has to be installed on the target machine.

> Note you can always switch between Web Server types when running your applications. Other than IIS configurations are self configuring so they just work. **IIS requires some pre-configuration** so that the Virtual, Security and Permissions are configured properly to run the Web site.

### Non-default IIS Web Site Selection
If you're installing to full IIS, you can optionally choose the **Web Site to install to**. By default the *Default Web Site* and `localhost` is used, but you can pick from the drop down to select to install on any of the sites available on your machine:

![](/images/ManagementConsole/SelectWebSite.png)

> Note: If you choose this option you'll need to fix up the URLs used to access the site to match the alternate domain name. You should also edit the startup URLs in `Launch.prg` and in `wcdemo.ini` (or `MyApp.ini` for any of your own projects).

---

[Step 2 - Web Virtual Path and Sample Configuration](VFPS://Topic/_S8G0Z22WJ)