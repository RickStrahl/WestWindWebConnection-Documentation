Executes a complete operating system command line with command arguments, using `ShellExecute()` command line resolution, but lets you express the command as a single command line that is the same as what you'd run from the Terminal. It also executes Application Protocols, URLs, and opens documents based on non-executable extensions.

> Executables are searched for along the OS path, so you can use `notepad` to launch NotePad without specifying a full path.

This function exists to allow for more natural command line syntax, so that you don't have to separate the command line and parameters. Applications that are in your OS path can be resolved without requiring a full path. 

Unlike FoxPro's Run command it also works with long paths and paths with spaces in it.

```foxpro
* Path resolution for notepad
ExecuteCommandLine("notepad .\config.fpw")

* Even simpler - resolves path relative to current path
ExecuteCommandLine("code .\")

* Explicit Path and Command Line (all one line!)
lcCommandLine = 
  ["C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\Common7\IDE\devenv.exe"
   "\WebConnectionProjects\wwThreads\wwThreads.sln"]
ExecuteCommandLine(lcCommandLine)
```

`ExecuteCommandLine()` also works with standard `ShellExecute()` 'command resolution' of Application Protocols and file name extensions:

```foxpro
ExecuteCommandLine("https://webconnection.west-wind.com")   && opens browser
ExecuteCommandLine("skype:3212223333")                      && opens skype
ExecuteCommandLine("c:\temp\test.pdf")                      && opens PDF viewer
```

While these last syntax values work, we'd recommend you use `ShellExecute()` just to be more obvious about the intent and reserve use of `ExecuteCommandLine()` for execution of executables.