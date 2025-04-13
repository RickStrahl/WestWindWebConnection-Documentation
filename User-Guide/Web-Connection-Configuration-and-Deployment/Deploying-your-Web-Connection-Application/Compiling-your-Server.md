In order to deploy your server the first step will be compilation.

If you created your application with the New Project Wizard you can simply do:

```foxpro
DO BLD_<yourProject>
```


This will compile the server and register it for interactive use **locally**. Alternately you can just compile your EXE file directly:

```foxpro
BUILD EXE <yourProject> FROM <yourProject>
```

> #### @icon-warning COM Compilation requires Admin Rights
> Web Connection servers are EXE COM servers by default and as such need to write COM registration information into the registry under the Local Machine key which requires Admin rights. To compile make sure you run `vfp9.exe` using 'Run As Administrator'.
>
>Alternately, if you are planning of running Web Connection in File Mode only or you need to run in non-admin mode you can temporarily comment out the `OLEPUBLIC` reference in `<yourProject>Main.prg` and the `<yourProject>Server` class. For example:  
>```foxpro
>DEFINE CLASS <yourProject>Server AS WWC_SERVER && OLEPUBLIC
>```

### Running your Server
Once compiled your server can now be run from Explorer by double clicking on the compiled EXE.

> ### Turning off te Desktop Window
> When you launch your EXE server it might be nice to disable the desktop window which you can do with the `ShowDesktopForm=on` option in `MyApp.ini`.

It can also be accessed as a COM object. To quickly test COM operation try this from the Command window:

```foxpro
oServer = CREATEOBJECT("<yourProject>.<yourProject>Server")
? oServer.ProcessHit("query_string=wwMaint~FastHit")
```

The first line should bring up your server as a window. The second line will simulate a request in the server and should return an HTTP response string.