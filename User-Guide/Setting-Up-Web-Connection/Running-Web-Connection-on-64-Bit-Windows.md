Depending on whether you are running the ISAPI module which is a **32 bit** component, or the .NET Managed Handler which **can run** in 64 bit or 32 bit mode, you have to configure the IIS Application Pool hosting your application for operation in 32 bit mode.

> #### @icon-info-circle Use the .NET Managed Hander
> Because the ISAPI module is a 32 bit DLL, you **have to** run the ISAPI handler in 32 bit mode. We recommend you use the .NET Managed Handler whenever possible as it can run either in 32 or 64 bit Application Pools, is more stable and efficient, easier to configure and has many more features than the ISAPI module. 

### IIS 7 and Later
IIS 7 and later uses Application Pools to configure and isolate individual application processes via a customizable Host Process. Application Pools can be configured to run in 64 bit (default on 64 bit servers) or 32 bit mode.

* **Web Connection ISAPI DLL (32 bit)**  
The Web Connection ISAPI DLL is a 32 bit application and **requires** that the Web Server runs in 32 bit mode.

* **The Web Connection Managed Handler (32 bit and 64 bit using .NET)** The Web Connection .NET Managed Handler is a multi-targeted .NET assembly and **can** run in 64 bit or 32 bit modes. We still recommend 32 bit mode, but either will work. 

#### Configure Application Pool 32 bit Setting
Application Pools configure a IIS Host process. Each AppPool can house 1 or more Web 'applications' that use the same host process and you can configure this Application Host process via the **Application Pools** setting in the IIS Management Console.

To set the Application Pool to 32 bit:

* Open the IIS Management Console
* Go to Application Pools
* Find your Application Pool instance
* Change the Enable 32-bit Application to `True`

![](//images/ManagementConsole/Enable64BitAppPool.png)

Note that if you are running on a 32 bit server this option is not available.

### Enable 32 Bit operation on Windows 2003 Server/XP
You can set the 64 bit option using the IIS Admin tools using the following code in VFP:

```foxpro
DO CONSOLE WITH "ENABLE64BIT"
```

to turn it off:

```foxpro
DO CONSOLE WITH "ENABLE64BIT","OFF"
```

You can also run CONSOLE.EXE from the Windows Command prompt:

```
CONSOLE.EXE ENABLE64BIT
CONSOLE.EXE ENABLE64BIT OFF
```

### The Web Connection .NET Managed Handler can run in 64 bit Mode
Web Connection 5.20 and later also ships with a .NET HttpHandler Handler that is capable of running in 64 bit mode natively without requiring any changes to the Web Server. The Handler supports operation both in file and COM modes.

The Handler works with ASP.NET 2.0 on IIS 6 or as a native HttpHandler in IIS 7.0 and it works without any configuration changes both in 32 and 64 bit mode.

For more information please check out [Using the Web Connection Managed Handler](vfps://Topic/_22Q0YKDNS).

### DCOM Configuration on 64 bit Windows
DCOM configuration on 64 Bit Windows requires that you run DCOMCNFG in a 32 bit mode so that 32 bit components are properly configured. To do this you can run the following from the Windows Run box (Start Menu -> Run):

<pre>mmc comexp.msc /32</pre>

Failing to do so *may* cause problems with security configuration. Specifically if you don't see the option to run the DCOM component on the local machine you need to run the above. It's not always the case but this has caused several of our customers problems. Running the above always fixes these problems on 64 bit machines.