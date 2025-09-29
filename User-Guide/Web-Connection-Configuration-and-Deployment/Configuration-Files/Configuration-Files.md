Web Connection is configured through a few configuration files. These files configure each of Web Connection's components for communication with each other. The following files are used:

* **web.config** *(IIS/IIS Express)*  
This is an ASP.NET configuration file that configures IIS applications. This file contains IIS, ASP.NET and Web Connection configuration information if you are using the .NET Managed Handler. Several webConnection specific sections configure Web Connection configuration.  
*auto-created during setup or first run*

* **WebConnectionWebServerSettings.xml** *(Web Connection .NET Web Server)*  
This file holds all of the Web Connection configuration setting when running the local Web Connection Web Server or when running the Web Connection Web Server inside of IIS. When running local only this file is used for configuration. If running in IIS, it's used in combination with `web.config` which is then used for IIS/ASP.NET specific settings only.  
*auto-created on setup or first run*

* **Your Application's Server INI file** *(YourApp.ini)*   
Each Web Connection FoxPro server application you build uses a copy of this INI file to configure its startup environment. The file sits in the same folder as the project and compiled EXE file. This INI file contains application specific settings which you can optionally add to using the various `wwServerConfig` objects to persist any custom application properties.

* **wconnect_override.h**  
This header file configures Web Connection **at compile time** - it's not required for a compiled and deployed application at runtime. The file contains a number of constants that determine class names, buffer sizes and the like. It's based on `wconnect.h` in the Web Connection install directory, which it overrides for your specific project and path. **Never make changes to wconnect.h as it it is overwritten in updates!**