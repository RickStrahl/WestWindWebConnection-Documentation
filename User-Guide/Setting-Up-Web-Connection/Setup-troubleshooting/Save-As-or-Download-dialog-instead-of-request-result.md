If you are hitting Web Connection URLs and you are getting download file dialogs, it means that the Web Server is not properly configured to handle the script extension. It means that the Web server is trying to load as a static resource and serve it to you generically.

In recent version of IIS you are more likely to see a **404 Not Found** than this error.

To fix this problem, make sure that Web Connection configuration is run. Specifically you need to configure the script map that you are accessing and map it to the Web Connection .NET or ISAPI module in the `bin` folder. 

### IIS Handler Configuration via web.config
The handler mappings can be explicitly done in the IIS Management Console, but it's actually much easier to simply cut and paste the mappings directly into web.config as follows. Both the .NET and ISAPI modules use slightly different definitions. 

##### .NET Module

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name=".wwd_wconnect-module" path="*.wwd" verb="*" 
           type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" 
           preCondition="integratedMode" />
      <add name=".wc_wconnect-module" path="*.wc" verb="*"                 
           type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" 
           resourceType="Unspecified" requireAccess="Script" preCondition="integratedMode" />
    </handlers>
  </system.webServer>
</configuration>
```

Because the .NET Module uses a known class name of the webconnectionmodule.dll in the /bin folder, .NET module script maps defined can be easily copied to a new location without changes.

##### ISAPI Handler

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <handlers>
       <add name="WC Handler" path="*.wc" verb="*" modules="IsapiModule" 
            scriptProcessor="C:\wconnect\web\wconnect\bin\wc.dll" resourceType="Unspecified" 
            preCondition="bitness32" />    
      <add name="WWD Handler" path="*.wwd" verb="*" modules="IsapiModule" 
           scriptProcessor="C:\wconnect\web\bin\wc.dll" resourceType="Unspecified" 
            preCondition="bitness32" />
    </handlers>
  </system.webServer>
</configuration>
```

The ISAPI handler requires a physical path to the DLL in order to find the ISAPI handler and so when the location is moved the file path has to be updated.