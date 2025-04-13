`ShellExecute()` is an API wrapper around the Windows API with the same name. Using `ShellExecute()` you can:

* Open Web URLs
* Open files by filename in their associated application 
* Open local folders in Explorer
* Open Application Protocol Handlers
* Open OS applications that are on the OS path without a path

Here are some examples:

```foxpro
ShellExecute("https://west-wind.com")     && opens in Web Browser
ShellExecute("c:\temp")                   && opens folder in Explorer
ShellExecute("c:\temp\test.pdf")          && opens PDF in PDF viewer
ShellExecute("skype:8083215555")          && Opens Skype
ShellExecute("markdownmonster:untitled")  && Opens associated application
ShellExecute("code")                      && Opens VS Code (if installed)
```

ShellExecute can also execute commands with a complex commandline. Unlike the `RUN` command `ShellExecute()` works with long filenames or filenames with spaces in them.

```foxpro
ShellExecute("c:\Program Files (x86)\Markdown Monster\mm.exe", ;
             "OPEN","c:\temp",; 
             ["c:\temp\Getting Started With MM.md" ".\test.md"])
```

For command line execution you might find `ExecuteCommandline()` a little simpler as you can write it the same way as you would from the Terminal as a single line:

```foxpro
ExecuteCommandLine([c:\Program Files\Markdown Monster\mm.exe "c:\temp\Getting Started With MM.md"])
```