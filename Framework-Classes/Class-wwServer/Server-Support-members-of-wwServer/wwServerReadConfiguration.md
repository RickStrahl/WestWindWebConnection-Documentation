This method reads information out of the configuration INI file of your Web Connection application.
The INI file typically has the same name as the project, so the sample INI file is called wcDemo.ini.

The INI file consists of a Main section which sets the server's operational parameters and an optional
section for each sub application.

Reads the server's configuration data from the application
INI file. The file contains the server's starup settings:

```ini
[main]
tempfilepath=c:\temp\
template=wc_
logtofile=1
saverequestfiles=0
showserverform=1
showstatus=1
usemts=0
scriptmode=3
timerinterval=200

[wwdemo]
datapath=d:\wwapps\wc3\wwDemo\
htmlpagepath=d:\westwind\wconnect\
```

In this example, wwDemo is a sub application with two configuration values.  Configuration values in sub applications are very useful for server side configuration and typically handle things like paths and other values that may differ between development and online applications.

Web Connection implements these settings via a special class, which you have to create and configure for each sub-application that requires configuration values. These values are read from the INI file on server startup and are then available for the duration that the server is running. You can access these in your application code like this:

```
lcHTMLPath = THIS.oServer.oConfig.owwDemo.cHTMLPagePath
```

In order for the above to work you need to create a custom Server config object, which adds individual objects for each sub application. The following code comes from the wcDemoMain.prg file that ships with Web Connection to configure the demo app and the wwDemo sub-application:

```foxpro
DEFINE CLASS wcDemoConfig as wwServerConfig

owwDemo = .NULL.
owwMaint = .NULL.

FUNCTION Init

THIS.owwDemo = CREATE("wwDemoConfig")

ENDFUNC

ENDDEFINE

DEFINE CLASS wwDemoConfig as wwConfig

cHTMLPagePath = "d:\westwind\wconnect\"
cDATAPath = "d:\wwapps\wcx\wwDemo\"

ENDDEFINE
```

To add any other subapplications simply create another object and add it to the wcDemoConfig class in the same fashion. The actual config object simply needs to have properties for each value that needs to be saved in the INI. 

> @icon-info-circle Note that the value must start with a type prefix which is dropped when written to the INI file.*

To instantiate this class you need to then call it in your SetServerEnvironment startup routine:

```foxpro
*** Override config with our own object which 
*** has sub-configs for applications.
*** wcDemoConfig is defined at the bottom of this PRG
THIS.oConfig = CREATE("wcDemoConfig")
THIS.oConfig.cFileName = THIS.cAppIniFile
```