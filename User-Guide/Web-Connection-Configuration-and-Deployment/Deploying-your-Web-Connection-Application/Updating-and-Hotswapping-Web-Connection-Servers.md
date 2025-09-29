The Web Connection .NET and .NET Core modules include a built-in mechanism for **hot swapping an existing COM server while the server is running**. This mechanism allows updating of an executable on a live server without taking the server offline first. The actual hot swapping process takes a few seconds at most. 

At a low level hot swapping works like this:

* Upload a new EXE using an upload link
* File is stored in a known file location: `UpdateExe` config key
* Once the file is there you can hot swap
* Put requests on hold
* Unload running servers
* Hot swap the new EXE into the running Exe: `ExeFile` config key
* Take requests off hold
* Reload file servers - COM servers will auto-restart

That's a lot of steps, but the process is automated in two ways:

* **Web Site Administration Update Buttons**  
The Server Administration page has a couple of links that allow you to **Upload Exe** and **Update Exe** the server interactively.
* **A generated `bld_YourServer.prg` Script**  
This option lets you run a local FoxPro file, to publish your EXE directly from your local machine.

> #### @icon-info-circle Write Permissions and File Paths
> In order for files to upload and swap, the account that's running the IIS Application Pool has to have full access rights in the `Deploy` folder. Also make sure that `ExeFile` and `UpdateExe` point to valid files. Both files support `~\` Web site relative paths. For example:
>```xml
> <webConnectionConfiguration>
>    <add key="ExeFile" value="~\..\deploy\wcdemo.exe" />
>    <add key="UpdateFile" value="~\..\deploy\wcdemo_Update.exe" />
> </webConnectionConfiguration>
> ```  

> #### @icon-info-circle Only Updates the Server Exe
> These two mechanisms only publish the actual EXE file. If you have other files that need to be updated - like support DLLs or secondary executables - you have to figure out some other way to get those published in which case you probably have to manually stop and restart the Web site.

  
### Adminstration Web Interface
Using the Web Connection administration page, there are two links that allow you to upload a new server EXE and then hot swap it.

You can find these links `Administrate.wc` page in the middle of the page:

![Update Exe](/images/misc/UpdateExe.png)

Make sure the upload EXE file matches your application's actual EXE file. The name has to match because this is the file that will replace your original server. 

Click on Upload Server Exe. You are prompted to pick an EXE file to upload. Pick your local compiled Exe and go.

If the upload succeeds the `YourServer_update.exe` now exists on the server. Click on the **Update Server Exe** button to hot swap the update file into the main exe file. The link handles shutting down servers, putting the server briefly on hold and then hot swapping the files. When done the server is then taken off hold and ready to receive new requests.

When it's all said and done you should see a page like this:

![](/images/misc/HotSwappedFileComplete.png)

### Using the `bld_YourExe.prg` Script
Another option is to use the `bld_YourServer.prg` script  with a parameter of `.T.`:

```foxpro
do bld_YourServer with .T.
```

This script file was generated when a new project was created and you can modify this file to add additional functionality and update things like passwords and server locations. You have to edit the file to change the default target server and default user name to log into your server to allow the upload to work.

The script performs all the steps we saw in the Web Interface in one continuous operation, uploading the file, and then hotswapping it.

When done you get a confirmation or error page.

> #### @icon-lightbulb Always start with the Web Page!
> When you run a server update for the first time I recommend you use the Web Page first to ensure that the update process is working. If that works as expected, then and only then should you use the FoxPro script to publish as you get more information on the Web page if something goes wrong.


### Configuration Settings
The update process is controlled via the `ExeFile` and `UpdateExe` configuration settings in the `web.config` (.NET Module) and `WebConnectionWebServerSettings.xml` (.NET Core) files. 

**In web.config:** (.NET Module)

```xml
<webConnectionConfiguration>
  <add key="ExeFile" value="~\..\deploy\wcdemo.exe" />
  <add key="UpdateFile" value="~\..\deploy\wcdemo_Update.exe" />
</webConnectionConfiguration>
```

**In WebConnectionWebServerSettings.xml:** (.NET Core Module)

```xml
<WebConnectionWebServerSettings xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
    <ExeFile>~\..\deploy\Wwthreads.exe</ExeFile>
    <UpdateFile>~\..\deploy\Wwthreads_Update.exe</UpdateFile>
</WebConnectionWebServerSettings>
```

The paths here use Web relative virtual paths via `~\` which map to the project's `Web` folder. You can also use a full path, or a completely relative path, but the above syntax is most portable.


### Automating the process with Bld_YourServer.prg
When you create a new project Web Connection automatically builds a `bld_YourServer.prg`  where `YourServer` is the name of your project. That script compiles your server and if that works sends the file to the server using the same **Upload Exe** and **Update Exe** links we saw on the Web Site.

The script is plain PRG code, so you can review what it does:

* Builds the server
* Registers the COM server
* Optionally registers for DCOM
* Uploads the file
* Hotswaps the file

If any of these steps fail the process is aborted.

Here's the relevant code from this file (this example assumes the EXE name and virtual are `WebDemo`):

```foxpro
DO wwUtils
DO wwHttp

*** Configure these options to perform tasks automatically
EXE_FILE		  =		  	"Webdemo"
DCOM_ProgId       =			"Webdemo.WebdemoServer"
DCOM_UserId		  =			"Interactive User"

*** Server Update Urls - fix these to point at your production Server/Virtual
HTTP_UPLOADURL    =         "http://localhost/Webdemo/wc.wc?_maintain~uploadexe"
HTTP_UPDATEURL 	  =         "http://localhost/Webdemo/wc.wc?_maintain~UpdateExe"

*** For ISAPI DLL use this UploadUrl (through Web Connection Server)
*HTTP_UPLOADURL    =         "http://localhost/Webdemo/wc.wc?wwmaint~FileUpload"

*** Optional - User name to pre-seed username dialog
WEB_USERNAME 	  =	  		StrExtract(sys(0),""," ")


lcUserNameHttp = InputForm(PADR(WEB_USERNAME,15),"Http Username","Code Update")
IF EMPTY(lcUsernameHttp)
  RETURN
ENDIF

lcPasswordHttp = GetPassword("Please enter your HTTP password") 
IF EMPTY(lcPasswordHttp)
  RETURN
ENDIF  
     
WAIT WINDOW "Uploading file to server." + CHR(13) + ;
           "This could take a bit..." NOWAIT

loHttp = CREATEOBJECT("wwHttp")
IF !EMPTY(lcUsernameHttp)
   loHttp.cUsername = lcUsernameHttp
   loHttp.cPassword = lcPasswordHttp
ENDIF
loHttp.nHttpPostMode = 2
loHttp.AddPostKey("File",FULLPATH(EXE_FILE + ".exe"),.T.)
lcHtml = loHttp.HttpGet(HTTP_UPLOADURL)
IF (loHttp.nError != 0) OR ATC("File has been uploaded",lcHtml) = 0
  MESSAGEBOX("Upload failed." + CHR(13) + ;
             loHttp.cErrorMsg)
  RETURN
ENDIF

WAIT WINDOW "File upload completed..." nowait

lcURL = InputForm(PADR(HTTP_UPDATEURL,100),"URL to update Exe","Update")

wait window nowait "Updating Exe File on Web Server..."
loHttp = CREATEOBJECT("wwHttp")
lcHTML = loHttp.HTTPGet(lcUrl,lcUserNameHttp, lcPasswordHttp)

wait clear
IF !"Exe Updated" $ lcHTML
  ShowHTML(lchTML)
ELSE
  MESSAGEBOX("Update completed",64,"Web Connection Server Code Update")
ENDIF
```

You can customize this file as needed, and you can perform additional deploy operations. For example, if you need to push additional files up to the server, you can potentially use the FTP publishing tools to publish those extra files as well.

### Manually?
You can of course do all of this manually, but if you do you're going to have to go through all the steps described at the top of this article. 

Doing it manually involves manually copying files to the server, taking the site off line, copying in files, then restarting.

There are timing issues here too because the binaries are locked while the server is running so you have to upload to a temporary file then swap the files when the server is online.

While this is all possible the process is very error prone and likely will take a lot longer keeping the server offline for more time than necessary. 

Not recommended, but it's available if you need to do it the hard way or because you need to publish through some Administrative procedures.