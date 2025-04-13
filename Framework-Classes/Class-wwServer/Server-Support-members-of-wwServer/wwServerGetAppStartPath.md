This method is used to determine the server's startup location. The
startup location is used to manage data and code access for the 
server so the server can find the appropriate files. Behavior varies
depending on how the server is running. 

cAppStartPath is Set in the server's Init and can be overridden by
you in SetServerEnvironment. After SetServerEnvironment is called
the server then adds the path to the current Fox PATH and in certain
RUN modes changes directory to this path.

**COM EXE or Standalone EXE or VFP IDE operation**  
Server does a CD (THIS.cAppStartPath) changing path to this directory.

**COM DLL**  
Server only does a SET PATH TO (THIS.cAppStartPath). You should use
the SetServerProperties method to explicitly add all paths the app 
will be accessing manually with code like this:

```foxpro
DO PATH WITH THIS.cAppStartPath + "data"
DO PATH WITH THIS.cAppStartPath + "DemoData"
```

Location of the directory is determined in the following order:

1. Out of the registry at the following key if it exists:

    **HKLM\SOFTWARE\West Wind Technologies\Web Connection\Servers\<serverclass>**  
   
   If this key exists [cAppStartPath](vfps://Topic/wwServer%3A%3AcAppStartPath) will be set to this value.
   
2. From *Application.ServerName* which returns the fully qualified
   path to the COM EXE or COM DLL. For standalone applications,
   SYS(16,0) is parsed. The function that performs this task is the
   generic GetAppStartPath in wwUtils.prg.
   
To update the registry setting bring up the server window, click on
Status and update the startup path. Then click on Save Settings to
write the new value into the registry.