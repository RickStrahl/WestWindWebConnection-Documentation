The oConfig member maps to the server's configuration object for this project. 

You can access any custom configuration settings you've defined on the object through the following interfaces from within a wwProcess class:

```foxpro
THIS.oConfig.cHtmlPagePath
Config.HtmlPagePath
```

oConfig and Config are mapped if a configuration class with the same name as the class exist (for the wwDemo class it would Server.oConfig.owwDemo). These are mapped to [wwServerConfig](vfps://Topic/_0F20TYJV3) instances that act as a master class. The class has to map to:

```foxpro
Server.oConfig.o<ProcessName>
```

For example, for the wwDemo class the hierarchy looks like this:

```foxpro
Server.oConfig.owwDemo
```

The class must be defined and hooked up to the wwServerConfig instance of the application. The new Process Wizard automatically creates these classes which are declared and hooked up at the bottom of your application's main PRG file (<myapp>Main.prg). Please check there to see how this works.

Use the configuration class to hold any commonly configurable settings that get persisted into the application's INI file.