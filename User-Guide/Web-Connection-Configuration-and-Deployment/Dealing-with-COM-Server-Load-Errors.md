Handling startup errors in live applications - especially COM servers - can be tricky. Startup errors are special in Web Connection in that there are no active requests yet. Unlike Web Connection's normal error pipeline which manages request errors and logs them and goes on to the next request, startup errors occur before the server is full initialized, so a different approach is needed.


## COM Server Registration Errors
First things first. The first thing is that your **COM server has to be registered on the target machine** and has to have the appropriate permissions to launch.

You register COM servers with:

```foxpro
YourServerExe.exe /regserver
```

> #### @icon-warning Admin Rights Required
> COM Registration requires Administrative right. Make sure you **Run As Administrator** when launching the command line window.

## Enable Unattended Mode
Make sure you enable unattended mode in your server by setting the `UnattendedComMode` flag in your Web Connection's FoxPro Server `yourApp.ini` file:

```ini
UnAttendedComMode=On
```

This ensures that your COM server will not pop open any blocking UI when operating in COM mode. Errors like file open dialogs or UI popups will fail and generate an error instead, but will not hang your application.

## Test your Server
Once your server is registered make sure it works by testing it. If you have FoxPro installed you can do the following:

```foxpro
loServer = CREATEOBJECT("yourApp.YourAppServer")
? loServer.ProcessHit("PHYSICAL_PATH=c:\WebSites\WebSite1\test.wc")
loServer = NULL  && release
```

You can also do the following from a Powershell Admin command prompt:

```ps
$server = new-object -comObject 'wcdemo.wcdemoServer'
$server.ProcessHit('PHYSICAL_PATH=c:\WebSites\WebSite1\test.wc')
[System.Runtime.InteropServices.Marshal]::ReleaseComObject( $server )
```
> You may have to set Powershell Execution policy to allow execution of scripts:
> ```ps
> Set-ExecutionPolicy Bypass -Scope CurrentUser
> ```

Either of these should bring up your FoxPro COM server and print out some message. If the file doesn't exist or Web Connection can't process it it'll still produce some sort of HTTP response.

If you get a **load error** here it means most likely the server is not registered properly or you don't have permissions to launch the server. 

If the server **appears to start but then hangs** it's very likely that you have a startup error in your `OnInit()` or `OnLoad()` code in the server. Make sure `UnattendedComMode=On` and if that's the case check the `wcTracelog.txt` and/or set up custom startup error tracing (see more in [Startup Error Tracing](VFPS://Topic/_4JU02V1L7)).

## Do you need to use DCOMCnfg with COM? Probably not!
In order to launch your COM Server you need to ensure the server allows DCOM servers to launch. By default Administrator accounts as well as SYSTEM and NETWORK service have rights to COM servers. Most other types of accounts do not. If you are using the latter **only then should you need to use DCOMCnfg**.

### Pass-through Security from IIS Application Pool
The easiest way to ensure COM servers can be launched with the appropriate rights, is to set up **IIS Application Pool Impersonation** to the appropriate account that has rights to access your local file system, data files, network etc. and then **not do any the DCOM configuration at all**. By not doing DCOM configuration you are essentially setting the user to the `Launching User` which is the Application Pool identity.

To set up User Impersonation for the IIS Application Pool:

![](IMAGES/misc/ApplicationPoolIdentity.png)

DCOM's User Identity defaults to the **Launching User** which means it inherits the User Identity from the calling process which is the IIS Application Pool as configured above.

This way DCOM just inherits the IIS impersonation and **nothing else needs to be configured**. This also leaves all user configuration in one place at the IIS level instead of in multiple places. If you've previously configured DCOM Impersonation you can switch it back by setting the Impersonation to *The Launching User*.

Web Connection defaults to the `SYSTEM` account when it configures IIS which has full launching rights on the local machine. You can also use `NETWORK SERVICE`  which has more limited rights, but **you have to make sure you set permissions on all resources so that that account can read/write as needed**. You can also use any other user account you choose, but using other non-Admin accounts will require that you explicitly configure DCOM Config.

If you choose a different user, we recommend you use an Admin user to avoid COM loading conflicts. If you use either of these, there's no need for DCOM configuration of any kind. 

### When DCOM Configuration may be Required
DCOM configuration **may** be required if you use a lower rights account in which case you may have to manually add that user explicitly to the DCOM security settings of your COM server. This is increasingly rare however - don't touch DCOMCnfg unless you run into COM permission errors.

In summary - let IIS handle your user impersonation for you and have the COM server inherit the account information from IIS.

> #### @icon-warning Don't use DCOM Interactive Impersonation in Production
> `Interactive` is a special Windows account that maps to the interactive, logged on user. Interactive is the only Windows account that allows for the Web Connection server to be visible on the desktop, but it's a bad idea to run a server in this fashion as **it requires that a user is logged on at all times**. If you log off or get logged off due to the Windows desktop timeout and the server needs to restart, **servers will fail to load** since there is no Interactive user.
>
> **Just say no to Interactive in a production environment!**

## Load Error
There are a host of errors that can occur - lets go through a few of them.

### COM Registration Errors
If you try to load your COM server from the Admin page (Load Servers) and you get the following errors:

* Invalid Class String
* Invalid Com ProgId

your COM server is not registered properly or you have the wrong ProgId in your **web.config** or **wc.ini** file. Double check the **ComServerProgId** (web.config) or the **[AutomationServers]** section (wc.ini) to make sure the ProgIds are correct.

If that's correct try loading the COM server from FoxPro or VbScript and make sure it can launch that way first. 

### COM Server Permission Problems
If you get errors that relate to permissions:

* Access is denied
* Invalid Permissions

You may have to explicitly set permissions on your COM server in DCOMCNFG. Specifically open DCOM config in 32 bit mode:

```ps
MMC comexp.msc /32
```

and then find your ProgId server instance. Then set the **Launch** and **Access** permissions on the Security tab and make sure to add the account that you are using for the IIS application pool. While in DCOMCNFG also double check to make sure that the Impersonation is set to **Launching User** so that you will inherit the Web servers account.

## COM Server Startup Problems
If you don't get any immediate error messages when the server loads, there are two types of things that can go wrong:

* Startup Initialization Code Errors
* DCOM Access Permissions

### DCOM Access Permissions
If you are not using SYSTEM or INTERACTIVE USER for your Application Pool Identity and/or DCOM identity, it's possible that you are missing explicit Access rights in DCOM.

To troubleshoot this issue I recommend you start with using SYSTEM or INTERACTIVE USER (if you are logged on the machine) to **first ensure that the server works**.

## For Startup Errors check wcTraceLog.txt
If you're application fails to load completely or loads but doesn't respond it's likely you have startup errors that can't be logged to the regular Web Connection request log. If that's the case check wcTraceLog.txt which logs exceptions that occur during startup. For more info on `wcTraceLog.txt` see [Startup Error Tracing](VFPS://Topic/_4JU02V1L7).