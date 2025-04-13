This method fires the Update Exe or script application by taking a full command line to the external app or script. Runs the app and then QUITs the current instance of Visual FoxPro.

If you're using the CodeUpdate.exe file compiled from the CodeUpdate.prg snippet your command line looks like this:

Example (all on one line):```foxpro
CodeUpdate "wwReader70.exe" 
	"d:\wwReader\codeupdate\wwReader_update.exe /auto d:\wwReader" 
	"West Wind Message Reader"
```


```foxpro
loUpdate.RunUpdateExe([CodeUpdate "wwReader70.exe" "] + ;
                   SYS(5) + CURDIR() +;
                   [codeupdate\wwReader_Update.exe /auto ] + ;
                   SYS(5) + CURDIR() + [" ] + ;
                   ["West Wind Message Reader"])
```


Using the Windows Scripting Host sample:

```foxpro
loUpdate.RunUpdateExe([WSCRIPT CodeUpdate.vbs "wwReader70.exe" "] + ;
                   SYS(5) + CURDIR() +;
                   [codeupdate\wwReader_Update.exe /auto ] + ;
                   SYS(5) + CURDIR() + [" ] + ;
                   ["West Wind Message Reader"])
```