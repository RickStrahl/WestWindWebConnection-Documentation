The Web Connection Handler Administration page and the Web Connection Middleware Administration pages show you the Web Server configuration. The .NET or ISAPI modules provide the low level interface to the Web Server and this page shows all the configured settings as well as the running servers. 

The page also allows you to manage the servers loaded by loading and unloading of servers, switching the operations mode and putting the server into maintenance mode.

Here's what the Web Connection Module Administration page looks like (.NET module version):

![](/images/ManagementConsole/ModuleAdministrationPage.png)

As you can see the admin form displays the active servers running (both in File mode and COM mode) as well as list of configuration settings that largely match the configuration settings in `web.config` (for the .NET module) or `wc.ini` for the ISAPI module.

### Server Loading
This section controls Web Connection's processing mode of either **File based** or **COM mode**. You can use the **Switch** button to toggle between the two modes. Note that in order to run in COM mode your server has to be compiled as an EXE and must be registered on the server that it's running on using `yourServer.exe /regserver`.

The **Server Count** setting lets you dynamically set the number of servers that are loaded when running in COM mode or when auto-starting file servers. You can change the value here and Web Connection automatically unloads and reloads the servers with the new server count.

The **Hold Requests** option allows you temporarily suspend processing of requests against the server with the exception of authenticated users. This allows you effectively take the server offline temporarily while a message that the server is on hold is displayed.

The **Script Timeout** determines how long the Web Connection module waits for a request against your Web Connection Server to complete. If the request is not completed in the timeout period the request is aborted and an error message for request timeout created. This setting matched the **Timeout** configuration setting in `web.config` or `wc.ini`.

### Version and Configuration File
This section displays the version of the Web Connection Module (.NET or ISAPI) and the active configuration file (web.config for .NET or wc.ini for ISAPI) that the module is using. 

> Make sure the configuration file lives in the folder you expect it to. The folder it lives in determines the location of your Web site that is launching the application so this path allows verifying this path.

### Web Server
This section is specific to file based messaging in Web Connection and shows the **Temp File Path** where messaging files are being written to by the module using the prefix specified by the **Temp File Prefix**. 

The path and template **have to match** the path that is configured Web Connection server's `yourApp.ini` file in order for the module and FoxPro Web Connection server to communicate with each other. Further make sure that both the **Server Account** (shown below) and the Web Connection exe have full permissions in this folder.

### COM based Messaging
This section is specific to running Web Connection in COM mode. The **COM ProgId** shows the COM progId configured in the configuration and should match the COM server that is registered on the server you are trying to execute on. It matches the `ComServerProgId` setting in web.config.

Make sure the server is registered on the target machine with `myServer.exe /regserver` in order to be able to launch the COM server.

> By default the COM server runs under the security context of the **Server Account** (shown below). Make sure that this account will have rights to launch the COM server on the local machine.

### Web Connection Server Executable
This section shows the settings for the **ExeFile** and **UpdateExeFile** settings in web.config/wc.ini and determines the location of the exe file. These settings are used by the module to read version information as well as allowing you to upload a new server executable and hot swap it in COM mode.

If the **ExeFile** is set correctly and the EXE exists, the module will provide version information for the EXE.

### Security
This section shows the **Admin Account** that is allowed access. This value can be empty (no security), ANY (any logged in user) or a Comma Delimited list of specific usernames (including server names) that have access to admin features.

The **Authenticated User** shows what the currently user account is which is going to be the account you logged in with to get to this admin page. The **Auth Mode** displays what authentication mechanism was used by the server to authenticate.

Finally the **Server Account** shows the underlying account that Web Server is running under - for IIS this means the Application Pool user identity. This is important as this account determines the right that your Web application has. If you run your COM server or file based server with default launching user permissions, your FoxPro Web Connnection Server also inherits this user account and the related permissions. Make sure this account is an account that has appropriate rights.

The Server Account maps to the IIS Application Pool User Impersonation setting, and if you need to change this value change the IIS Application Pool user is the place to do it.

### Server List and Management
At the top of the page you see the list of active servers that are currently running. 


Depending on whether you run in COM or File Mode this list is compiled differently:

* **COM Mode**
In COM Mode the Web Connection Module tracks the attached COM server in the Thread pool and the server list simply reflects the request stats accumulated in this server list. Because Web Connection manages the list it is quite accurate and Web Connection can easily start and stop servers.
![](/images/ManagementConsole/ServerListModulePage.png)


* **File Mode**   
In File Mode Web Connection gets a list of processes that match the server's EXE file name and uses the Windows Process APIs to retrieve all running processes with that file name as well as retrieving process information for each of the running processes. Web Connection does not have an active list of the running processes, so it can't directly monitor these instances. The start and stop operations are limited to effectively killing existing instances and restarting them.
![](/images/ManagementConsole/ServerFileListModules.png)

As you can see the two lists for file and COM servers look slightly different but overall the behavior is similar.

### Re-read Configuration
This option re-reads the configuration file (web.config or wc.ini) and reloads the current page with the updated settings. If you have to make changes to either configuration this is a good option to reload settings without having to restart the Web server.

> web.config changes are immediately applied and don't require any reloading. ASP.NET automatically detects changes and reloads the active application when the changes are saved to disk.

### Load Servers
This option causes all servers to be explicitly loaded. If servers are already running they are first unloaded, then a new set is reloaded.

For COM based operation the thread pool is loaded with the number of instances specified in the configuration. In File mode the EXEs are executed as standalone EXEs from within the IIS and the active IIS Application Pool user account.

### Unload Servers
Unloads all running server instances. In COM Mode the thread pool is unloaded by releasing each of the COM objects individually. In File Mode the externally running EXEs are terminated by executing a Process Kill operation after a short timeout.

### Load One Server
This operation is similar to loading all servers except only a single server is loaded. This step first unloads all running servers, then loads up a single server instance.
Deploy\Temp folder. This can be useful if you need to push files to the server for initial installation and if you don't want to use RDP's slow file transfer or install an FTP server on the server.

This is a useful feature to ensure that only a single instance of a server is running so you can administrative tasks inside of your FoxPro server, so you can perform `EXCLUSIVE` data operations that otherwise would not be possible when multiple instances have the same files open.

### Upload Server Exe
This operation allows you to pick a file on your local machine and upload it to the server using the `UpdateExeFile` file name. Basically you can upload a new server after you've made changes. The file can then be hotswapped using the next option.

> #### @icon-info-circle IIS Request Upload Size Limits
> Keep in mind that IIS's default request limits are pretty low so if you upload a large file it's likely to fail. Make sure set the `<httpRuntime maxRequestLength="10000000">` attribute in `web.config` to allow for a larger number of bytes to be uploaded. The default size is somewhere 2mb.

### Update Server Exe
This operation hotswaps servers by copying the file specified in `UpdateFile` to `ExeFile`. This operation also shuts down all servers before the update operation, and then restarts them automatically after complete. This process should only take a few seconds.

### Upload Zip File
This option allows you to upload an arbitrary Zip file to the server in the `deploy\temp` folder. This can be useful if you don't have a good way to push files to the server otherwise, so you can avoid slow RDP file transfer or having to install an FTP server. It's an easy way to get installation files pushed to a server including your binary files. This request runs without a FoxPro Web Connection server having to be up and running yet so you can push your Web site up via Web Deploy, then upload a Zip file with your binary and support files.