Web Connection is configured through a few configuration files. These files configure each of Web Connection's components for communication with each other. The following files are used:

* **web.config**
This is an ASP.NET configuration file that configures IIS applications. This file contains IIS, ASP.NET and Web Connection configuration information if you are using the .NET Managed Handler. Several webConnection specific sections configure Web Connection configuration.

* **wc.ini**  
If you're not using the .NET Handler but rather use the ISAPI Handler then `wc.ini` into `\bin` folder of your Web site configures Web Connection. wc.ini configures the ISAPI DLL and resides in the same path as the DLL with the same name as the DLL. This file contains settings like where to put temp files, where to find your application server and which servers to load during COM operation.

* **Your Application's Server INI file**  
Each Web Connection server you build uses a copy of this INI file to configure its startup environment. This INI file also contains application specific settings which you can freely add using the various wwServerConfig objects to persist any custom properties.

* **wconnect.h**  
This header file configures Web Connection at compile time. The file contains a number of constants that determine class names, buffer sizes and the like. It also contains a most important DEBUGMODE flag that lets you switch between debug and development modes which results in different error handling characteristics.