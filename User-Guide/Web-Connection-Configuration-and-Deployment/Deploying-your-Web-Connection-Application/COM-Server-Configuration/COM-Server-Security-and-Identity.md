Perhaps the most confusing aspect about using COM mode in Web Connection is security. When running in COM mode, Web Connection COM Servers are invoked from within IIS as **out of process EXE servers**. As such the EXE COM server has to be launched under a specific user account using DCOM (any EXE server is by definition a DCOM server even if local).

There are two kinds of security involved for COM servers:  

* **IIS Application Pool Identity**  
An Application Pool is the out of process application that hosts one or more IIS web sites. It's the parent process in which the Web Connection module runs - but not your FoxPro application. By default the account that IIS uses has no rights and it will not work for Web Connection, so you will have to change the Application Pool Identity to something else.  
*We recommend using an Admin or SYSTEM account due to COM server management requirements*

* **Your FoxPro COM Server Impersonation**  
In COM mode Web Connection runs your FoxPro server as an Out of Process COM server which is an EXE that has its own security context separately from the IIS Application Pool. By default, DCOM (out of process COM servers) use the **Launching User** which inherits the security of the parent process or in this case the IIS APplication Pool. Optionally you can use DCOMCnfg to explicitly specify the FoxPro server a different set of permissions. If you use a non-Admin or SYSTEM account you have to give explicit Launch and Activation access to the COM server.  
*We recommend you leave this as Launching User to inherit the security context from the AppPool*

The confusion comes from the fact that DCOM by default allows only certain Windows accounts to launch EXE COM objects:

* The Interactive User (requires a desktop/console logon - dev only)
* SYSTEM
* A local Administrator Account

If you use Admin/SYSTEM **No DCOM Configuration is required**. 

If you use non-Admin/non-SYSTEM accounts you have to **explicitly enable Launch and Activation rights** for the individual server (at the COM Server level) or the user (at the Computer Level).

### COM Permissions for Server Management: Admin or SYSTEM preferred
For production applications in COM mode, we recommend using an Admin or SYSTEM account as it's the only account that can properly manage the COM lifetime. 
 
This isn't ideal from a security perspective as it is permissive, but if you want reliable COM cycling especially in very busy systems with many instances or an app with multi-second requests, Admin rights are required to shut down errant COM instances that won't shut down directly via COM release.

> #### @icon-warning Admin Account preferred
> Admin accounts are not required, but... if you run without in COM mode you will lose the ability to shut down (Task Kill) any instances that were not able to shut via COM release operations. This means requests that haven't completed yet or servers that are hung or previously crashed and otherwise disconnected from COM. 
> 
> This can manifest with extra COM Servers showing up in your Windows Task list that are never released.
>
> By running as Admin Web Connection can hard kill these instances after COM release and before launching new servers, which unfortunately is not possible with a non-admin account. 
 

### COM Loading and Permissions
Any account other than these, requires **explicit DCOM permission configuration** either by setting an explicit user identity on the specific COM server's launch permissions for specific users or by setting machine wide DCOM permissions for the user.

If the DCOM permissions are not lax enough, and you try to invoke an application that is running in COM mode you might get the following error:

![](IMAGES/Howitworks/ComLoadError.png)

There are other errors like *COM Server ProgId not found* which may also be due to the fact that the registry can't be accessed to retrieve the COM server id.

> If you get launch failures - **make sure that your COM Server is registered** on the machine by testing the COM server on the machine locally via Powershell or other COM client [as described here](https://webconnection.west-wind.com/docs/_s901dvuwg.htm#testing-your-com-server).

If that is the case you will need to explicitly configure the Web Connection COM Server using DCOMConfig and enable explicit DCOM permissions for that account. 

Before we look at how that's done, let's talk about how security for COM invocation works in IIS.

## How to Control COM Server Identity
The user identity that is used to launch the COM server can come from two differing mechanisms:

* **Pass through security from IIS via IIS Application Pool Identity**  
* **Explicit DCOM Configuration**

### IIS Application Pool Identity
When IIS runs, it uses a specific user account that is configured for the Web Site's Application Pool. When your COM server is invoked it's called using that user account's security context and the COM server by default will launch in that same security context unless explicitly configured via DCOMCnfg.

This identity is configured in the IIS Application Configuration dialog in the IIS Administration Console:

* Open Application Pools
* Select your Application Pool (or create one)
* Advanced Settings (on sidebar on the right)
* Scroll to **Process Model->Identity**

![](IMAGES/misc/ApplicationPoolAdvanced.png)

You can pick from several standard system identities or provide an explicit existing user account with user name and password.

> The Application Pool Identity Setting is a one time configuration: You set it when you create the Application Pool or change the Identity. It's set it and leave it!

When you create a Web Connection Project or if you run the Web Connection configuration without any specific account information, Web Connection defaults to the local machine **SYSTEM** account (`LocalSystem` in the dialog), which gives you full access on the local machine. This will make the application work on the local machine and most likely give you all the permissions you need, but **it's essentially full control over the machine**, so you need to be careful and it's not recommended for a production machine in case of a compromise!

**SYSTEM** is not ideal for a deployed application, but for development this is ideal as it requires no additional configuration. For a deployed application, it's best to create a **custom account** and give it **exactly the rights that it needs** with access only to the application's folders and - if a non-admin account - explicit launch rights for the DCOM server.

When you create a new Application Pool in IIS manually, it defaults to **ApplicationPoolIdentity** which is a **very low rights generated account**. **Do not use this account for Web Connection** - it will fail as it lacks local file access rights or the ability to create COM objects. It's also not a regular user account so to change permission on it is very difficult. Always change this account to one of the Built-in or Custom accounts.

## Explicit DCOM Configuration
If you're not using `INTERACTIVE`, `SYSTEM` or an `Administrator` mode user, you will need to explicitly configure the COM server with DCOM Config to provide at minimum permissions for that user to be able to launch a Web Connection server. Using `DCOMCnfg` enables the COM server for activation and invocation by the given user. Once you do set this up, the specified account then can activate and invoke the DCOM server that is your Web Connection application.

Explicit DCOM configuration always overrides automatic pass through security. However, regardless of which approach you use we recommend you **always run your IIS Application Pool in the Identity you need for your FoxPro COM server**. This is easier to manage and understand as the security for IIS and the COM Server are then one and the same since many of the settings are shared.



### Using DCOMCnfg
To run DCOMCNFG use the **32 bit** DCOMCNFG utility in Windows which lives in the **Component Services** applet. 

> #### @icon-warning Use the 32 Bit Version of DCOMCnfg!
> Make sure you run the **32 bit version** of the DCOMCnfg tool

To use the 32 bit version type the the following into the **Windows Run box** (as Administrator) or an Admin Command Prompt:

```
MMC comexp.msc /32
```

![](IMAGES/misc/DcomCnfgMain.png)

> #### @icon-info-circle DCOM Server Names
> If you have multiple COM objects marked as `OLEPUBLIC` in your project it's possible that the name of this object will pop up instead of <MyApp>.<MyApp>Server.

* Scroll through the list and find your server name in the list usually (<yourProject>.<yourproject>Server) or if you used the Fox Project's server naming features the name of the server. 
 
* Once you find your server select it and choose *Properties*

Alternately you can set permissions for a specific user account. To do this:

* Go My Computer
* Go to Security
* Then set the Default Access Permissions
* Then set the Default Launch Permissions
* Set both to the desired account

The advantage of this latter approach is that this is a one-time setting that is unaffected by COM server updates, while the per-server config **may** be affected if the COM interface of the server changes (ie. the typelibrary generates a new COM ClassId - rare and only if you make changes to the main server class interface).

### Setting Server Identity - Don't do it unless you have to!
As mentioned DCOM servers have an Identity associated with them when they are launched. This can be a pass through identity from the host process (ie. the IIS Application Pool) or a specific user that you specify here.

* Under Server Properties
* Go to the Identity Tab and set the **Identity** to the desired account 

![](IMAGES/misc/dcomcnfg1.png)

The default for this setting is **The launching user** and we recommend that **you don't change this setting**, which uses the Identity passed down from the IIS Application Pool. Unless **you explicitly need to set an Identity that is different from the Application Pool** it's more consistent use pass-through security.

If you do pick a custom identity pick from the list of built-in accounts, or choose a local or domain user account from the account list.

> Note that the **Interactive User** is the only option that allows you see the Web Connection Server on the desktop when running as a COM object. Any other account runs in a non-console context that doesn't project onto the current user's desktop. 
>
> The Web Connection local build process via `do bld_MyAppServer.prg` automatically sets the DCOM identity to **Interactive User** so the server is visible during development in COM mode. For production the identity is not set by default. You can change the build setting in the scripted PRG file if you prefer a different setting.

#### DCOM Permissions - The Important Part
Next you'll need to set the Launch and Access permissions for the COM Server - again, only if the account used isn't `INTERACTIVE`, `SYSTEM` or an `Administrator` mode account. 

There are two ways this can be done:

* **At the Machine Level**
This allows you to give specific users launch and activation rights, so this becomes a global setting that can be set once and doesn't need to be reset unless permissions need to be changed.

* **At the Component Level**
This gives you most control as you can control each component individually and assign users to it. But it also requires you to reconfigure DCOM each time the COM registration for a component changes. 

**Machine Level**  

* Open DCOMCnfg (32 bit!)
* Go to the **My Computer** node
* Open **Properties Tab Context Menu**
* Open **COM Security**
* Edit **Default Access Permissions**
* Edit **Default Launch Permissions**
* Adding user with **Local Access**

**Component Level**

* Open DCOMCnfg (32 bit!)
* Find your COM component in the list
* Double click or Properties Context Menu
* Go to the **Security** Tab
* Customize **Launch and Activation Permissions**
* Customize **Access Permissions**
* Add your User Account to the User list
* Make sure Local Access is checked

Here's what this looks like for the component level DCOM configuration:

![](IMAGES/misc/dcomcnfg2.png)

Once this is set, the account you selected should now be able to launch from IIS. 

> You may have to restart the Application Pool in order to see this change as Windows caches credential permissions in Processes until they are restarted or some lengthy timeout.

## What Account Should you Use?
There's no single answer to this question as it depends on your application scenario. Is security important? If so use a non-admin custom account. Is this a dev machine? Using `SYSTEM` is easiest.

Here's a little more detail on some common options.

### What needs to be secured?
The main consideration for security is: What rights does my Web Connection Application need?

The following permissions are needed by your Web Connection application server typically using a standard Web Connection Project folder hierarchy:

* Read Access in the `\web` folder
* Optional Write Access in `\web` folder for:
    * `web.config` - if you want to remote update settings
    * Dynamic Script uploads and compilation of scripts
* Read/Write Access in the `\deploy` folder (recursively including `\deploy\temp`)
* Read/Write Access in any Data or Document Folders your app uses
* DCOM permissions (depends on account)

### Accounts
There are a few accounts that are of note for the purposes of Security:

#### SYSTEM - Local System
This is a **full rights** account on the local machine and this is the default that Web Connection creates. This is easiest account to use with Web Connection because **it just works** without further configuration. But it's also **insecure** because it has full rights on the machine and if your application should get compromised this would allow full access to the machine.

SYSTEM is a built in fixed account and you can't customize permissions for it. It's great for quick get up and go, but if you need to customize permissions, use a Custom Account.

* Generic Windows Account
* Insecure
* Easy configuration - works without any config
* Doesn't require DCOMCnfg

#### NETWORK SERVICE
Network service is a low rights built-in Windows account. By default it has no access to to local folders, and it requires explicit DCOM configuration. Web Connection by default configures `NETWORK SERVICE` disk permissions for Web Connection projects and the Configuration Wizard (which you can customize).

If you want to use a generic account and explicitly give additional rights, this is a good account to use. Because Web Connection has some default configuration for this account it's also easy to work with - Web Connection automatically sets folder access permissions as well as can automatically register for DCOM Configuration using Server Config functionality (requires a small configuration change).

* Generic Windows Account 
* Secure by default
* Requires setting disk permissions
* Requires DCOMCnfg in order to Access Registry for COM Launch

#### Administrator Account or Non Admin Account.
For deployed applications it's generally best to create a brand new local account specifically for the application and set the explicit permissions required for the application. You can pretty much remove all disk rights from that user and then add in just those folders that the application has access to - typically the Web Connection Project folder plus any data folders. This will limit your potential exposure to security issues.

**Administrative Account**

* Insecure
* Easy configuration - works without any config
* Doesn't require DCOMCnfg

**Non-Admininstrator Account**

* Secure by default
* Requires setting disk permissions
* Requires DCOMCnfg

### Auto-Registration for DCOM via Configuration Script Customization
When your COM server changes its interface signature you'll need to re-register the DCOM configuration as well, so automating this process as part of the setup is a good idea.

The Web Connection Project configuration includes a COM server registration section that by default has DCOM configuration commented out. However you can add a call `DCOMLaunchPermissions()` for your specific user that will automatically re-register the COM server and then apply the DCOM permission as part of the `MyApp_ServerConfig.prg` configuration:

```foxpro
*** COM Server Registration
lcExe = lcAppName + ".exe"
IF FILE(lcExe)
   RUN /n4 &lcExe /regserver

   *** Optionally set DCOM permission - only set if needed!
   *** requires that DComLaunchPermissions.exe is available in .\tools\
   DCOMLaunchPermissions(lcAppName + "." + lcAppName + "Server","NETWORKSERVICE")
   * DCOMLaunchPermissions(lcAppName + "." + lcAppName + "Server","username","password")
ENDIF
```

To run this you would use this from the FoxPro Command Window:

```foxpro
do Myapp_ServerConfig
```

or from a compiled application from the Windows Command or Powershell Prompt

```ps
.\MyApp.exe /CONFIG
```