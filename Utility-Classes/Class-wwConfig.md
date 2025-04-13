***Saving and restoring configuration information the easy way with a single method call and simple property assignments.***  

This flexible object is used to persist configuration information as an XML file, an INI file or as a single XML value into the registry. This object basically persists the configuration object to a permanent location using the [Save](vfps://Topic/wwConfig%3A%3ASave) method, allowing transparent restoration of an object's state using the [Load](vfps://Topic/wwConfig%3A%3ALoad) method. This object can be efficiently used as a configuation manager for any application that needs to store configuration values in a persistent format. The object itself can be loaded once at application startup and saved when the application shuts down with two simple method calls with the object available for the duration of the application using global object reference or as part of an application object.

In its simple form you can define new values to be persisted simply by adding methods to a subclass of wwConfig:

```foxpro
DEFINE CLASS CustConfig as wwConfig

cFileName = "CustConfig.ini"
cMode = "INI"

*** Persist properties
cDataPath = ".\data"
cHTMLPath = ".\webpages"
cAdminUser = "username"
lSendEmailOnError = 1

ENDDEFINE
```

To save the settings from this object you'd simply call it's Save method:

```foxpro
oConfig = CREATEOBJECT("CustConfig")

oConfig.cDataPath = "\webapps\data\"
oConfig.lSendEmailOnError = .F.

oConfig.Save()
```

This would generate an INI file CustConfig.ini like this:

```ini
[config]
DataPath=\webapps\data
HTMLPath=.\webpages
AdminUser=username
SendEmailOnError=0
```

To load values from the INI file you'd use:

```foxpro
oConfig = CREATEOBJECT("CustConfig")
oConfig.Load()

? oConfig.cDataPath
IF oConfig.lSendEmailOnError
   SendEmail()
ENDIF
```

To add more values simply add another property to the object. Any values in the INI file that match the properties of the object (minus the type prefix character) are read into to properties with the Load() method and written out with the Save() method.

wwConfig determines how to load and save values based on the setting of the [cMode](vfps://Topic/wwConfig%3A%3AcMode) property, which can be set to a string value of INI, XML or REGISTRY.

wwConfig can persist nested objects. All object properties attached to an instance of wwConfig will be persisted and restored along with the 'main' properties. The following illustrates how to create a nested object:

```foxpro
DEFINE CLASS wcDemoConfig as wwConfig

owwDemo = .NULL.
owwMaint = .NULL.
cCustomProperty = "Some Value"
nCustomValue = 10

FUNCTION Init

*** Add nested objects - hierarchical persistance
THIS.owwDemo = CREATE("wwDemoConfig")
THIS.owwMaint = CREATE("wwDemoConfig")

ENDFUNC

ENDDEFINE

*** Custom Config objects which can be created as subobjects
*** of the config object.
*** In the process class this can be references as:
***
*** lcPath = THIS.oServer.oConfig.owwDemo.cDataPath
***
*** You can add any number of custom config objects
*** here and simply add them to the server!!!
DEFINE CLASS wwDemoConfig as wwConfig

cHTMLPagePath = "d:\westwind\wconnect\"
cDATAPath = "d:\wwapps\wc3\wwDemo\"

ENDDEFINE
```

This generates:
```ini
[config]
CustomProperty=Some value
CustomValue=10

[wwdemo]
HTMLPagepath=d:\westwind\wconnect\
Datapath=d:\wwapps\wc3\wwdemo\

[wwmaint]
;*** no properties</pre>
```

## Requirements
All property names must start with a single character type identifier. The type identifier is removed when the property is persisted and reassembled when loaded back in. It's crucial that every property of this object has a prefix or else you'll see truncated property names in the persisted files/documents (it will still work though - just looks funny).

## Formats
wwConfig can persist in two formats using XML or an INI file. Use the INI file method if you can't guarantee that the IE XML parser is available on the target machine.

**XML Format:**  

```xml
<?xml version="1.0"?>
<wcdemo>
	<main>
		<tempfilepath>c:\temp\</tempfilepath>
		<template>WC_</template>
		<logtofile>True</logtofile>
		<saverequestfiles>False</saverequestfiles>
		<showserverform>True</showserverform>
		<showstatus>True</showstatus>
		<usemts>False</usemts>
		<scriptmode>3</scriptmode>
		<timerinterval>175</timerinterval>
		<wwdemo>
			<datapath>d:\wwapps\wc3\wwDemo\</datapath>
			<htmlpagepath>d:\westwind\wconnect\</htmlpagepath>
		</wwdemo>
		<wwmaint>
			<datapath>d:\wwapps\wc3\wwDemo\</datapath>
			<htmlpagepath>d:\westwind\wconnect\</htmlpagepath>
		</wwmaint>
	</main>
</wcdemo>
</pre>
```

**Ini Format:**  

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

[wwmaint]
datapath=d:\wwapps\wc3\wwDemo\
htmlpagepath=d:\westwind\wconnect\
```

## Usage
Using the class is very easy. Use the following steps:

* Create a subclass of wwConfig and add any custom properties that need to be saved.
* Call the appropriate Save() method of the class to persist the settings.
* Call the appropriate Load() method to load the settings back into your subclassed wwConfig object.
* If you want nested objects, add the object properties and then load the objects in the Init of the class.

```foxpro
o=create("MyConfig")
o.cFileName = "Config.xml"

IF .F.
    o.cFileName = "Config.ini"
    o.cSubName = "Ini File Test"
    o.cMode = "INI"
    ? o.Save()
ENDIF    
IF .T.
    o.cFileName = "Config.ini"
    o.cMode = "INI"
    ? o.Load()
    ? o.cSubName
    ? o.oTest.cHTMLPagePath
ENDIF
IF .F.
	o.cSubName = "Default Application - Not yet set"
	o.nTimerInterval = 100
	o.cTemplate="cfg_"
      o.cMode = "XML"
	? o.Save() 
	modi comm config.xml
ENDIF
IF .F.
    o.cMode = "XML"
    o.Load()
ENDIF

IF .F.
	o.cSubName = "Test Application"
	o.nTimerInterval = 100
      o.cMode = "REGISTRY"

   o.cRegPath = "Software\West Wind Technologies\TestConfig"
   o.cRegNode = "Parameters"
   ? o.Save()
ENDIF
IF .F.   
   o.cRegPath = "Software\West Wind Technologies\TestConfig"
   o.cRegNode = "Parameters"
   o.cMode="REGISTRY"
   
   ? o.Load()
ENDIF
```