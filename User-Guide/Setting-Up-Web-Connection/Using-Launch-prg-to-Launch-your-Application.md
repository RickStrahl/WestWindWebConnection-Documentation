Starting with Web Connection 7.0 a simplified project launcher script called `Launch.prg` file is **generated for new projects**. This file can be used to quickly launch your Web Connection applications using a number of different start modes. Rather than using the traditional `DO MyAppMain.prg` you can use `Launch.prg` to:

* Launch your FoxPro Server
* Launch a separate Web Server like IIS Express
* Launch your Web Browser

all in one single command step. 

You can use Launch.prg to work with these servers and combinations:

* IIS
* IISEXPRESS
* WEBCONNECTIONWEBSERVER

### Launch Modes
`Launch.prg` supports a number of different combinations of modes. 

* **IIS**
Launches the application without any special server startup since IIS is always up and running. Opens the browser for the IIS URL. Urls include a virtual:  
`http://localhost/MyApp`.

* **IISEXPRESS**  
Launches the IIS Express WebServer on the default port `7000`. If already running it'll try to launch again but this has no effect as it's already running. Then starts your Web Connection application and opens a Web Browser on the url:  
`http://localhost:7000`.

* **WEBCONNECTIONWEBSERVER** OR **DOTNETCORE**  
Launches the Web Connection Web Server which is a self-contained, re-distributable, Console based Web Server that you can run from the command line. It's based on .NET Core and other than that requirement fully self-contained. The server is part of the project, which means it can be distributed with your project and allows running your Server locally (or over the Web) without having to install a separate server.
 
* **NONE** or **SERVER**  
Launches your FoxPro server **without launching any servers or opening a browser**.

* **HELP**  
Documentation link that opens this Web page

* **Called with no Parameters**  
This mode will default to either IIS or IISExpress mode depending on which Web Server you chose during installation. You can modify `Launch.prg` to set a different startup type to use as your default.

> #### @icon-info-circle What about: `DO AppMain.prg`?
> Although `launch.prg` makes it easier to launch applications for the first time to get servers and browsers opened in one step, the traditional direct method of launching your server with `DO MyAppMain.prg` still works just fine and in fact is still a good way to launch once your server and browser are already open and running.

### Updating an Old Application to use Launch.prg
Applications prior to Web Connection 7.05 either didn't have a `Launch.prg` or had a more limited version. So to take advantage of the above features you need can easily create a new `Launch.prg` file into your existing project.

To do this:

* Use the Console to create a New Project
* Go to the project folder and copy `Launch.prg` into your existing project
* Modify the startup parameters in `Launch.prg`

All of the necessary parameters for `Launch.prg` are defined at the top of the program so you can easily change the code with the values for your specific project.

The code that needs to be adjusted is the following:

```foxpro
*** Generated Defaults
lcAppName = "wwThreads"
lcVirtual = "wwThreads"   && or "" for root site
lcScriptMap = "wwt"
lcWcPath = ADDBS("C:\WEBCONNECTION\FOX\")
lcWebPath = LOWER(FULLPATH("..\web"))
llIisExpress = .F.
```

In this case I updated my Message Board application to use `Launch.prg`. The changes I made are:

* Change `lcVirtual` and `lcAppName` to `wwThreads` 
* Change the scriptmap to `wwt`

And that's it. 

> Note that `lcAppName` is the name of your project, while `lcVirtual` is the name of your Virtual directory (or empty (`""`) for a root site) in IIS (even if you are not using IIS). In most cases these are the same but they can be different for projects with multiple process classes.

Note you can customize the `Launch.prg`. This file is generated for your project and won't be changed after it's created. 

To update to a newer version (if Web Connection changes behavior), you can always compare your version against the `Launch.prg` in the root of the install folder. You can use a diffing tool like [BeyondCompare](https://www.scootersoftware.com/) to compare for changes.