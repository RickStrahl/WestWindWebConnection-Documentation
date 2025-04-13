Most administrative features of Web Connection are accessible the Web Connection Administration interface which is available via `/admin/Administration.wc` in your Web Connection application which provides access to most Web Connection configuration features. This pages shows all settings and lets you change a number of them.

> Note: Older version prior to v7.10 and the ISAPI handler do not support this `Administration.wc` as a URL and you'll have to use the `/admin/admin.aspx` page as your launch page and then Access the **Web Connection Module Administration** link.

Administrative links break down into two groups:

* **[Web Connection Module Configuration](vfps://Topic/_S8X08H3KS)**  
These settings control how the low level Web server .NET, .NET Core or ISAPI modules are configured to interact with the Web Server and communicate to the Web Connection server. This form shows operational settings and lets you see active servers running. This controls the operational mode, number of instances, 

* **Web Connection Server operations**  
The Web Connection server supports a number of maintenance requests that run in FoxPro code. This includes log managements, local application configuration settings, file reindexing etc.

The admin page can be reached with <a href="http://localhost/wconnect/admin/Administration.wc" target="top">/admin/Administration.wc</a> and  looks as follows:

![](//images/ManagementConsole/ModuleAdministrationPage.png)


> #### @icon-warning Admin Page Security
> The Admin page contains sensitive links that allow you to manage and shutdown the application. For this reason you **do not want to accidentally allow outside access to the page**. Make sure the page is locked down properly. For more info please see: [Securing your Web Application](VFPS://Topic/_0J40WN2LX).
>
> If not authenticated, the page displays a warning: Access from a **local machine** just get a warning message, but access from a remote machine not display the admin links.
>
> ![](//images/misc/adminpageaccessdenied.png)



### Log Files
This section lets you review various log files in Web Connection. 

#### Web Connection Server Log
This is shows the Web Connection server request log which (optionally) logs each request into a FoxPro (or Sql Server) table. You can browse recent entries in this log with this link. The table contains both a request log for each request (if enabled) as well as errors.

#### Web Connection Server Error Log
This shows a filtered list of the the same request log table, but is filtered to show only errors. The error log captures all requests that cause an exception during request processing and this link lets you browse that list.

#### Clear the Request Log
This link allows you to clear the request log, but you need to make sure that you have only one active server instance running for this request to work.

#### Web Connection Module Error Log
This link lets you see the raw `wcErrors.txt` file which holds errors that occur in the low level Web Connection module. These errors are either hard errors when the module fails, or a number of operational entries. From the page displayed you can also clear this text based log.

### Server Settings
This section allows you to review and optionally edit the Web Connection Server settings

#### Web Connection Server Settings
This link shows you many of Web Connection's internal server settings. Most of these settings come from the `yourServer.ini` file and the server's own running execution environment.

#### Edit Configuration Settings
> @icon-warning Careful with Editing web.config
> It's recommended that you use the server editing features only as an emergency step. Because some of the data is formatted XML in web.config it's easy to get invalid XML values into a document via unencoded special characters. If that happens it's possible to corrupt the config file. If possible edit web.config using a text editor on the server - only use the online editing feature in emergencies. It's however reasonably safe to edit the `yourServer.ini` file and `wc.ini` for ISAPI operation.

This form allows you to edit the `yourServer.ini` Web Connection configuration file remotely. These settings let you control various path related settings as well configuration setting for each of the process classes in the application.

Here you can also edit the `web.config` or `wc.ini` file which contain the Web Connection module configuration settings. 

### Server Resources
This section lets you reindex the Web Connection System files and manage script compilation and script modes for Web Connection Script templates.

#### Reindex wwSession File
This option packs and reindexes the Web Connection wwSession file. By default it removes all entries older than a day and then reindexes and packs the wwSession file. It's a good idea to run this operation occasionally to reduce overhead created by memo field bloat in FoxPro tables. If you're using Sql Server request logs and session this command does nothing.

#### Reindex Web Connection Sample Files
This option packs and reindexes and restores the Web Connection samples to their default settings. Mainly meant for the West Wind site, but useful if you play with the demos and you want to restore to a stable state. You can remove this option from the HTML if you don't use it.

#### Script Mode and Compilation
Web Connection Script templates (.wcs files by default) are HTML scripts with embedded FoxPro expressions and script blocks that compile down into a FoxPro PRG. Compilation can either by dynamic at runtime, or explicit using a compile link and you can choose to run your server with precompiled scripts which is more efficient. If you run with pre-compiled scripts you have to explicitly recompile the scripts when you update them on the server.

**Interpreted** mode runs scripts and checks if the script is newer than the compiled FXP and if it is recompiles it. Each time the script is requested it has to check the file stamp, which is relatively slow. To avoid the file stamp check you can run in **PreCompiled** mode which assumes an FXP file exists for all scripts which is faster. The two links let you switch between modes.

The input box and **Compile WCS Scripts** button allow you to compile your scripts on the server and create new .FXP files from the scripts so you can run with PreCompiled mode. Keep in mind that FoxPro caches FXP files, so compilation changes may not be visible in all instances unless you reload the FoxPro servers after compilation.

### Module Administration

#### Update Code online
This option allows you to upload a new executable to the server and replace the running executable on the server. You can use the **Upload** button to upload a new server EXE to the server.

Use the **Upload Server Exe** button causes to upload an updated Web Connection server EXE defined in the configuration setting's **UpdateExeFile** on the server. Once uploaded you can then use the **Swap Server Exe** to swap the uploaded EXE with the live server EXE specified in the **ExeFile** settings of the server.

> #### @icon-info-circle Updating File Based Servers
>In order to swap servers, servers have to be first unloaded. In COM mode the **Swap Server** button automatically unloads COM servers, holds requests then copies the new file. For file based servers you manually have to put servers on hold, unload servers, run the Update Code link, then take servers off hold and restart servers.

### Restart Application
The final two links allow you to restart the IIS Web Server and or reboot the entire machine. Use these links with care. Both operations are triggered using the IIS Restart utility and require that the account that the IIS application is running under is using an Administrator account identity.