Web Connection ships with the West Wind Message Board sample application but the source code is separately stored on GitHub.

You can get the latest code from:

* [West Wind Message Board on Github](https://github.com/RickStrahl/West-Wind-Message-Board)

This is the same message board that runs at <a href="https://support.west-wind.com" target="top">support.west-wind.com</a>:

![](/images/misc/messageboard.png)

The Message Board source code is included as part of Web Connection, but it is also available on GitHub at:

* **[https://github.com/RickStrahl/West-Wind-Message-Board](https://github.com/RickStrahl/West-Wind-Message-Board)**

### Get it from GitHub
The full installation is contained in the Github repository. You can either **clone** or **fork** the repository, or else download the latest snapshot as a Zip file.

Pick a location to clone or unzip the repository and remember the path.

## Configuration
The message board runs as a separate standalone Web Connection application, so it needs to be executed in its own FoxPro session separate from the West Wind demos and if you are running the full version of IIS requires configuration.

### Location
The suggested location for your project is the `\WebConnectionProjects\wwthreads` folder, but any location really works.

The root folder contains a number of shortcuts that configure let you configure IIS, start IIS Express, run the application or let you open the Visual FoxPro IDE with the application loaded. The folder contains these helpers:

![](/images/misc/wwthreadsconfigurationfolderhelpers.png)


### Configure for IIS Operation

> ### @icon-info-circle IIS has to be installed and configured
> If you want to use IIS please make sure it is installed and properly configured. For more information see the following topic:
> 
> * [IIS Configuration for Web Connection](https://west-wind.com/webconnection/docs/_22f0xkbmq.htm)

You can install in two ways:

* Via Command Line
* Via FoxPro IDE

#### Install from Command Line 
If you're running the full version of IIS you can run the `Configure_IIS_RunAsAdministrator.bat` file in the root folder. As the name suggests make sure you start this using the `Run As Administrator` option on the Windows Explorer shortcut menu. 

This batch file runs `wwThreads CONFIG` to configure the IIS server for the `Web` directory. When done it'll launch the `wwThreads.exe` and opens a Web browser so you can see the application run.

#### Install from FoxPro IDE
You can also configure and run the application from within the FoxPro IDE. Make sure to launch FoxPro as an **Administrator** in order to handle the IIS configuration.

From a FoxPro Command window:

```foxpro
CD <installFolder>
DO wwThreads_ConfigureServer.prg

DO wwThreadsMain.prg
```

Once configured you should be able to navigate to:  
<a href="http://localhost/wwthreads/" target="top">http://localhost/wwthreads/</a> 

and then run `wwThreads.exe` in the `\Deploy` folder, or `DO wwThreadsMain.prg` from the FoxPro IDE in the same `\Deploy` folder.

If you need to tweak the server configuration settings for IIS, you can look at `wwthreads.ini` and change the `[ServerConfig]` settings to change the server where the configuration installs. If you make a change you can rerun `wwthreads.exe CONFIG` from Windows, or `DO wwThreads_ConfigureServer.prg` from the FoxPro IDE.

### Using IIS Express instead of IIS
If you just want to run the sample without configuring IIS, you can run with IIS Express assuming [IIS Express is installed](VFPS://Topic/_3NJ01RJ5N). You can simply run the `Startup_IISExpress.bat` batch file which runs the Web Connection Console helper to configure IIS Express and start it in the `Web` folder on port `8081`.

When done the batch file launches `wwThreads.exe` and opens a Web browser so you can see the application run.

Once configured you can navigate to:  
[http://localhost:8081/](VFPS://Topic/)

If you want to launch IISExpress from within the Visual FoxPro IDE (see below) you can do:

```
DO CONSOLE WITH "LAUNCHIISEXPRESS","..\Web",8081
```

to start IISExpress in the Web folder. Note you might have to change the path to `console.exe` in the batch file.


### No Project File
The repository doesn't include the binary project file, instead it's encoded by [Christof Wollenhaupt's TwoFox](http://www.foxpert.com/downloads.htm) using GenXml/GenCode.

### Run the wwThreads inside of the Visual FoxPro IDE
If you want to run the Message Board inside of FoxPro so that you can check out the code use the **Start FoxPro IDE with wwThreads and Web Connection** shortcut link to launch FoxPro.

This launches FoxPro in the `Deploy` folder and explicitly uses the `config.fpw` to load up dependencies for paths to Web Connection.

To start running the application do:

```foxpro
DO wwThreadsMain
```
and you're off to the races. You can now step through the code and make changes.