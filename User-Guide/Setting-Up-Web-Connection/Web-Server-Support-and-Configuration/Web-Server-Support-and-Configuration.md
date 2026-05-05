Web Connection supports a number of different Web Servers. Click the link for installation and configuration instructions.

* **[Internet Information Server (IIS)](VFPS://Topic/_22F0XKBMQ)** <small>(production, optionally dev)</small>   
This is the built-in Windows Web Server which is typically used to run production applications on a live server but can also be used for local development. IIS is a Windows component, but it **has to be explicitly enabled and configured** and **requires Administrative Rights** to do so. IIS runs as a Windows Service and is **always on**.  
`launch("IIS")`

* **[IIS Express](VFPS://Topic/_3NJ01RJ5N)** <small>(dev only)</small>  
IIS Express is a small (5mb), **installable version of IIS** that runs as a standalone application. It doesn't use global configuration and doesn't require Administrative rights, doesn't run as a service and has to be explicitly installed and launched manually.  
`launch("IISEXPRESS")`

* **[Web Connection Web Server](VFPS://Topic/_5LW0YSXQ9)** <small>(dev, optional production)</small>  
This is a **self-contained, Web Connection specific Web Server built with .NET Core**, that ships as part of a Web Connection project. It's **redistributable** and allows you to run Web Connection applications without a Web Server installed. Like IIS Express it runs as a standalone application. This Web Server is a great choice for local development.  Requires the .NET Core Runtime and the server has to be explicitly started. This server can also be hosted inside of IIS.
`launch("WEBCONNECTIONWEBSERVER")`

Once your preferred server(s) is installed Web Connection can automatically handle starting the application and the Web server if it's not already running via the `Launch.prg` file that is provided for the demo, and for newly generated projects. 


### Which server is best?
It's important to note that **all** of the supported servers have the same core operational behaviors in regards to running your Web Connection application. There are no major differences in operation of your applications. Rather the different servers are mainly about **how** the Web server is run and administered, and what best matches your environment. It's also possible to easily switch between installed Web Servers during development using the Web Connection `launch()` command.

Some general recommendations:

* IIS is used in Production Web Sites on a Windows Server
* For local Development:
  * Web Connection Web Server or IIS Express have low system impact  
    and run as Root web sites
  * IIS can also be used locally, but...
      * IIS is more complex to setup and maintain on a client Windows machine
      * Requires Administrative rights to install and configure
      * Each  application also requires a separate virtual directory or Root Web Site in configuration
      * Web Site Configuration is not easily portable across machines - re-config is required
      
> #### @icon-info-circle Recommendations
> Use the **Web Connection Web Server** or **IIS Express** for local development and skip IIS on the local machine. The two self-contained servers make for quicker installation and is simply easier to work with as they make it easy to run root site Web sites without any of the IIS installation, local machine security and administration headaches.
> 
> For local development, we prefer the Web Connection Server, because it's a simple command line based application that also provides some visual feedback via the terminal when requests are processing. You can manually start and stop it, and `launch()` automatically launches it if not already running. The Web Connection Server is also re-distributable with your application, so it's possible to run your Web Connection application on a client computer without having to install a separate Web server (but you do need a .NET installation for which we provide an easy online-download installer).
>
> IIS for local machines makes sense only for specific compatibility checks, and for seeing exactly how your application will run on a deployed server.