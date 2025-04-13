This step allows you to configure one or more scriptmaps for your server.

![](IMAGES%2FManagementConsole%2Fconfigure3.png)

### Creating a script map
Scriptmaps map to the Web Connection handler (wc.dll or webconnectionmodule.dll). In the ScriptMap field you can specify one or more file extensions which will be used as scriptmaps. Simply type the extension(s) you'd like to use without the '.'. For a single extension just type the extension, otherwise separate multiple with a comma.

The Script DLL Path allows you to select a path for the ISAPI extension - typically wc.dll. This is required only in ISAPI mode.

Note that for the .NET module this path is not used and will still show wc.dll as the default so you can leave it set at its default if you use the .NET module. The .NET module doesn't link a script file explicitly and instead relies on a .NET handler mapping by the type in web.config.

You can also specify whether the script maps are local to the virtual directory or global to the Web site. It's recommended you create local scriptmaps to avoid confusion over which DLL is handling requests at the server level. 

<hr>
Go to [Step 4](vfps://Topic/Step%204%20-%20Configure%20COM%20settings)