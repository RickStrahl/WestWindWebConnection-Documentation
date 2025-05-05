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
  * IIS can also be used locally,   
    useful for final compatibility testing
  * IIS is more complex to setup/maintain for local development  
    as it requires a separate virtual directory or Root Web Site domain

> #### @icon-info-circle Recommendations
> Personally, I install IIS on the system and configure all new projects with it, even though I usually run my local development applications using the Web Connection Web Server. 
>
> This way IIS is configured and can be run for testing **if needed**, but for the bulk of development work I can use the Web Connection Web Server or IIS Express. Little more effort, but best of both worlds.
>
> If I configure a project for IIS, I usually change the `lcServerType` in `launch.prg` to my preferred local server (ie. `lcServerType="WebConnectionWebServer"`). 
>
> If I don't configure for IIS and later decide I need IIS, I can run `<myApp>_ServerConfig.prg` to configure IIS for the project.