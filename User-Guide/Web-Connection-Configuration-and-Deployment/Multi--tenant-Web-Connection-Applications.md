Multi-tenant applications are Web applications that use a single Web Connection server instance to service multiple Web clients using a separate Web site or Web virtual. Each **tenant** perceives that they are running their own Web site while in fact all the logic is handled by a single backend that switches the execution context at runtime based on the site or virtual or other tenant identifier.

### Understanding Web Connection Servers in IIS Applications
IIS segments Web sites or Virtual Directories into **Applications**. IIS segments into isolated application domains with their own private memory and execution space. This space isn't a process, but rather a sub-process using logical separation inside of a single process. You can have many applications in a single IIS Application Pool which is the host process (w3wp.exe) that can host one or more Web sites or virtuals. Each Site or Virtual is separate even though they may be running inside of the same process/Application Pool.

The layering goes like this:

```
w3wp  (worker process host)
    Application Pool 1
        Application 1
        Application 2
        Application 3
w3wp 
    Application Pool 2
        Application 1
        Application 2
```        

Note that an `Application` in this context is a Site or a Virtual mapped to an AppPool.An application pool maps to one w3wp worker process and each application pool supports multiple applications.

Multi-tenant applications effectively tend to jump this application boundary where you have a single Web Connection server, that processes requests from multiple IIS applications, identifying each application based on the domain or virtual path, or some other identifier that the application provides.

```
Application Pool
    Tenant 1
    Tenant 2
    Tenant 3
```

Remember the tenant can be either a Virtual directory or a full Web Site.

### Supported Multi-Application Scenarios
Web Connection has traditionally supported multi-tenant operation with the ISAPI module. Because the ISAPI module is a Win32 DLL it loads into an IIS application pool and the DLL instance is global to all applications (ie. site or virtual) in that application pool. Because of wc.dll only laods once no matter how many 'applications' are running in an application pool across multiple Sites or Virtuals.

Likewise file based processing in either ISAPI or COM supports running a single set of instances across multiple Web applications. Because file based operation uses files in a fixed location, as long as each application looks at the same file location the requests can be shared among multiple servers.

### Unsupported Multi-Application Scenario
What doesn't work is running the .NET Handler mode in COM where a single set of servers is supposed to handle multiple virtuals/sites. Because .NET applications are tied to an `AppDomain`  defined in each `Application`/virtual/site, a single set of COM servers cannot serve requests for multiple virtuals/sites. Each virtual or site needs its own corresponding set of server instances.

For most applications this is totally fine. It only becomes an issue in typical multi-tenant applications when you need to serve multiple virtuals with a single set of COM servers.

In summary accessing a single set of servers from multiple applications:

#### Works

* ISAPI Handler
* .NET Handler with File Based Messaging

#### Doesn't work directly

* .NET Handler with COM

There are effective workarounds for the latter scenario.

### Creating Multi-Tenant Applications with the .NET Handler and COM  
Although the .NET COM scenario spanning multiple virtuals isn't directly supported there are a couple of efficient ways around creating multiple applications, and instead running a single Web application that isolates individual tenants.

#### Multiple Host Headers for a Single Web Site
IIS has support for host headers that allow you to assign multiple Domain names to a single Web site. You can assign each tenant their own domain and then check and map the domain name to each specific tenant in your application using `Request.GetServerName()` to retrieve the host-header domain name. To configure, host headers you assign multiple domain names to a single IIS Web site as this example demonstrates:

![](/images/misc/IISHostHeaderMultiTenant.png)

In this scenario every request goes through to a single Web site instance, but the domain name for each is different which allows you to effectively simulate multiple sites being served by a single IIS Application. 

This works great if tenants can run of a single set of markup templates/styles etc. If you need to customize you need to use a theming mechanism with custom folders that provide the customization (you can use the same approach as the next bullet item).

#### Single Site with many Subfolders
IIS Applications are scoped to a site or virtual boundary, but you can fool IIS by creating a folder hierarchy that's not using virtuals, but just uses plain sub-folders. To use this approach simply have a top level site of virtual and add folders for each tenenant. You don't configure each folder as an IIS application and IIS then treats these folder as part of the current application.

Each subfolder can be tenant specific and you can extract the folder out of the request path with `Request.GetLogicalPath()`. Each folder can then contain tenant specific templates, style sheets etc that customize the application.

![](/images/misc/IISMultitenent_folders.png)


### Workable
These two solutions are really workarounds and most likely they are only going to be required if you have existing applications that used the ISAPI COM functionality across virtuals before. These solutions do offer the ability to handle multiple tenants within the confines of a single application. But be aware that all of the tenants then share the same configuration and the same load characteristics. So if you load up 8 COM servers all 8 share the same `web.config` settings the same number of COM servers etc. which is usually what you want. 

This is also the standard way that .NET Applications are built - every 'application' has its own self-contained address space. If you break out of that, you get another application. In most situations that's perfectly fine. For multi-tenant applications you can use the workarounds above **if you want to use the Web Connection .NET module and COM**. If you use ISAPI or File based messaging then none of these choices have to be made as you can spread multiple tenants across multiple virtuals.