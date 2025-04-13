The `YourApp.ini` file contains server configuration values that configure Web Connection operation. This topic provides a quick overview of parameters and what they mean.

### File Based Messaging Settings

#### TempFilePath           
File based message temp file directory. Should match the path specified in the Web Server module's TempPath.

#### Template
File based message template prefix. Message files are written with this prefix which is what the application looks for to find pending requests

#### TimerInterval
Frequency of the time for file based operation in milliseconds. Determines how of the server checks for new message files. Recommend you set this to 150 or 200 and not much lower.

#### ScriptMode
The default server script compilation mode for MVC style [Script Pages](VFPS://Topic/_5RE0NXZJE).  
**1** - Dynamically compiled  
**2** - Precompiled (fxp files)

### Server Operation

#### DebugMode
If .T. the application stops on errors at the line of failure and pops up the debugger. If .F. the application's error handlers display error messages and log errors.

#### LiveReloadEnabled
LiveReload setting that allows for server side code changes to reload the Web Connection FoxPro server and force a browser refresh. |

#### MarkdownPagesSanitized  
Used only for Markdown pages (.md extension). If .T. strips script code, and any HTML elements from rendered Markdown. Recommended you do this for user entered markdown text.

#### MemUsage
Maximum application memory usage 'suggested' for FoxPro engine to use. Setting a lowish value here prevents VFP from trying to grab all memory on the machine. 



### Urls and File Links

#### ComReleaseUrl
The URL used to shut down running COM instances. This value is overridden once a request comes in, as the server provides the release URL, so this value is used only before a server has been hit.

#### HomeUrl                
The URL that is used when you click the **Browse** button on the server form. This will navigate to the site's home URL. This value is generated during configuration and matches the server you picked for the application (IIS port 80, IIS Express port 7200, Web Conneciton Web Server port 5200).

#### CodeEditorCommandLine 
An optional execution command line to launch an editor for this application. By default launches [Visual Studio Code](https://code.visualstudio.com/) if it is installed, but you can customize the command to use any editor. 

Visual Studio Code: `code .\`  
Visual Studio Solution: `C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\Common7\IDE\devenv.exe  "\WebConnectionProjects\wwThreads\wwThreads.sln"`

> The EXE should not be in quotes, but any parameters should be provided in double quotes.

### Logging

#### LogToFile
It .T. logs every request into the request log database.

#### SaveRequestFiles
If .T. writes the **current request data out to disk**, so the entire request and response can be reviewed in the **Display Request** on the Status form. **Only the last request can be viewed as each request overwrites the request data file.** This is a tool mainly for debugging your server during development. **Important**: Make sure you turn off this setting for production as it can slow down and crash multiple instances under load.

#### ShowServerForm
If .F. hides the server status form and never displays it. Note that in COM mode the server form may not be visible if you're not running under the logged in account.

####  ShowStatus
If .T. displays the last few current request Server Status form. Useful during development, but should be turned off for production applications.

#### ShowDesktopForm
If .T. shows the Server Window on the desktop rather than inside the Visual FoxPro desktop. You'll want to use this for production applications to keep the server window small. |