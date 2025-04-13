Web Connection provides two separate connector binaries that can interface with IIS:

* **.NET Managed Handler**  
This is new model that uses ASP.NET to manage the interface between IIS and your Web Connection Server. Because the handler is written in .NET the codebase is more adaptable and the code is actually more performant than the ISAPI module. The .NET module has also additional management features and uses consistent styling with the rest of Web Connection.

* **ISAPI Module**  
The ISAPI module was the original interface created to interface with IIS and it uses the ISAPI protocol implemented in a very low level Win32 DLL. ISAPI is very efficient, but because the codebase is in system level C++ code it's very difficult to manage and update. The ISAPI module includes a manually implemented COM pool manager which is not quite as efficient as the .NET implementation. The ISAPI module lacks some of the more intricate management features that have been implemented in the .NET handler.


### .NET and ISAPI usage is Transparent
The .NET Handler and ISAPI module can be used interchangeably as long as you use [script maps](VFPS://Topic/_0G013MUOO) for all Web Connection requests in your application which makes the URLs the same between either.

### Use .NET Handler whenever possible
**We highly recommend using the .NET Handler** because it's the better implementation for IIS 7 and later. It uses a high level C# .NET implementation that is more efficient, uses more stable COM base interfaces, provides better thread management, and failure management. The .NET implementation also has a number of additional features related to Web Connection Server management that aren't available in the ISAPI version. Finally in Version 6.x and later the .NET Module uses the new styling features to display content consistently with your application styling.

### Differences
For almost all scenarios the behavior of the .NET Module and ISAPI module are identical. They both handle file and COM based operation and forward exactly the same information from IIS to the Web Connection server.

The .NET Handler has some additional features for server lifetime management and also uses a cleaner UI that matches Web Connection's overall CSS styling and formatting.

##### One DLL serving multiple Virtuals
But there is one behavior difference that might require you to use the ISAPI module which involves multi-tenant applications. If you need to power multiple virtual/root directories with a single set of Web Connection Server instances, then the .NET Module cannot be used.

.NET applications are scoped to a single Virtual/Root Directory/Application. This means each application/virtual instance gets its own distinct .NET *Application Domain* and each virtual folder manages its own configuration and loads its very own set of COM servers. If you have multiple virtuals you'll end up with multiple set of COM servers. This behavior is inherent to ASP.NET applications and cannot be changed.

In contrast, ISAPI can use a single `wc.dll` instance to power multiple virtual directories with only one 'application' active as the ISAPI module runs outside the scope of a .NET AppDomain. As long as virtuals are housed in the same Application Pool (host process) in IIS, ISAPI you can power multiple virtual directories with a single copy of wc.dll and a single set of COM servers. 

##### .NET Module Workarounds for multi-hosted Sites
There are ways around this problem by using Web Sites with multiple domain names using IIS Host Headers or using multiple virtuals mapped to a single folder. For more info please see:

* [Multi-tenant Applications with Web Connection](VFPS://Topic/_4KS0MGBOE)

### Configuration
Either .NET or ISAPI handlers can be configured in the IIS Management Console or explicitly in the `web.config` file for the application.

##### .NET Handler Configuration (web.config):
```xml
<configuration>
  <system.webServer>
    <!-- IIS 7 Script Map Configuration -->
    <handlers>
      <add name=".wwd_wconnect-module" path="*.wwd" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" preCondition="integratedMode" />
      <add name=".wc_wconnect-module" path="*.wc" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" resourceType="Unspecified" requireAccess="Script" preCondition="integratedMode" />
    </handlers>
  </system.webServer>
</configuration>
```

To add new script maps you can simply copy one of the existing entries and rename the `name` and `path` attributes. Once configured the handler is synced to the application so if you move the project no changes are required.

##### ISAPI Module (web.config)
```xml
<configuration>
  <system.webServer>
    <handlers>
      <add name="WC Handler" path="*.wc" verb="*" modules="IsapiModule" scriptProcessor="C:\westwind\wconnect\wc.dll" resourceType="Unspecified" preCondition="bitness32" />    
      <add name="WWD Handler" path="*.wwd" verb="*" modules="IsapiModule" scriptProcessor="C:\westwind\wconnect\wc.dll" resourceType="Unspecified" preCondition="bitness32" />
    </handlers>
  </system.webServer>
</configuration>
```
To copy a new handler simply change the `name` and `path` attributes.

Note that these entries hard code the ISAPI `wc.dll` path. If you move the project to a new location you have to update the path to the DLL.