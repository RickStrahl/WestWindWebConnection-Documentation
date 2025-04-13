The wwCodeUpdate class provides a number of features including the ability to check a specific version resource in XML format and retrieving its content into class properties, downloading the update file to the local machine and a generic script file that can be used to swap the running application. The class helps manage this process making it very easy to plug in this functionality into any Fat client application.

Let's walk through the process.

The class provides an interface to check an online XML resource file which is in the following format:

```xml
<?xml version="1.0"?>
<codeupdate>
	<version>4.21</version>
	<fileurl>http://www.west-wind.com/files/updates/wwreader_update.exe</fileurl>
	<size>372</size>
   <usermessage>Version 4.21 of the West Wind Message Reader
is now available for download.</usermessage>
</codeupdate>
```

The class can then be used to access this XML resource and retrieve the content from it into properties of this  class. Once the version info is retrieved with the GetVersionInfo() method, you can download the update from the Web site into a specified location on the local machine with the DownloadUpdate() method. You then run the RunUpdateExe() method to run an external application - an Exe or Script that you create or use our default versions - to cause the application to be swapped. RunUpdateExe() launches the swapping application and quits the current application.

The process for the client application looks like this:

```foxpro
* SampleAppForm :: CheckForNewVersion()
LOCAL loUpdate, loAPI, lcURL, lnVersion

lcUrl = WWREADER_VERSIONUPDATEURL  && from .H file 
lnAppVersion = WWREADER_VERSION

loUpdate=CREATEOBJECT("wwCodeUpdate")

*** Url where to check for new versions (XML file)
loUpdate.cVersionUrl = lcUrl
loUpdate.cVersionType = "N"   && Numeric Type

*** Name of the application's Exe file to hotswap
loUpdate.cExeFile = "wwReader70.exe"

*** Returns the current available version from the XML file
*** Sets properties of the loUpdate object with the XML info
lnVersion = loUpdate.GetVersionInfo()
 
*** If no version we couldn't get the info from 
*** the Web Server
IF lnVersion = 0 
   *** Couldn't retrieve version info - just exit
   *** You can also display error info: loUpdate.cErrorMsg	
   RETURN 
ENDIF

*** Compare the version number against 
*** the app's version number - here a property of the form
*** Usually you'd use a .H setting, database field or Config object
IF lnVersion > lnAppVersion
   *** Display message for the user and ask to download
   IF MessageBox(loUpdate.cUserMessage + CHR(13) + CHR(13) +;
                 "Would you like to download and install it now? (" + ;
                 LTRIM(TRANSFORM(loUpdate.nFileSize,"999,999,999")) + ;
                 "kb)",32+4,"West Wind Technologies") = 6                

      IF !loUpdate.DownloadUpdate()
         MessageBox(loUpdate.cErrorMsg,48,"Download Error")
      ELSE 
         *** Shutdown VFP
         CLEAR EVENTS
         RELEASE THISFORM
         
         *** Run the generic CodeUpdate Exe and quit application
        loUpdate.RunUpdateExe(;
                   [WSCRIPT CodeUpdate.vbs "wwReader70.exe" "] + ;
                   SYS(5) + CURDIR() +;
                   [codeupdate\wwReader_Update.exe /auto ] + ;
                   SYS(5) + CURDIR() + [" ] + ;
                   ["West Wind Message Reader"])
       ENDIF
   ENDIF
ENDIF

RETURN
```

This code runs the CodeUpdate.vbs Windows Scripting Host file which swaps the Exe file and then restarts it. The code to this generic script looks like this:


```vbscript
if WScript.Arguments.Length < 3 then
   WScript.Echo("Invalid number of parameters passed." + vbcrlf + _
                "Syntax: WSCRIPT CodeUpdate.vbs <ExeFile> <UpdateFile> <ApplicationName>")
   Wscript.Quit(1)
end if

lcExe = WScript.Arguments(0)
lcUpdateExe = WScript.Arguments(1)
lcAppName = WScript.Arguments(2)

Set oShell = Wscript.CreateObject("Wscript.Shell")

'*** Wait for 5 seconds for app to shut down
lnResult = oShell.Popup("Waiting for application to shut down...    ",5,lcAppName + "Update              ",64)

'*** Run the Update Exe
oShell.Exec(lcUpdateExe)

lnResult = oShell.Popup("Updating Exe File... ",5,lcAppName + "Update              ",64)

'*** Check that the Exe exists
Set oFile = CreateObject("Scripting.FileSystemObject")
if not oFile.FileExists(lcExe) then
	lnResult = oShell.Popup("Updating Exe File......  ",5,lcAppName + "Update              ",64)
end if

oShell.Exec(lcExe)</pre>

The script expects three parameters:

WSCRIPT CodeUpdate.vbs "SampleApp.exe" 
                     "d:\sampleapp\codeupdate\sampleapp_update.exe /auto d:\sampleapp" 
```                     
### Sample Application

1.	**lcExeFile**  
The filename of the Exe file that runs. This is used to restart the Exe file when the update is complete. Example: SampleApp.exe (path is optional as long as it's visible from the current directory via PATH)
2.	**lcUpdateCommandLine**   
This is the full  command line that is used to run the self-extracting Zip file. You should use full paths for all paths provided, which includes the name of the Exe and the update directory. The /auto flag on the Zip file is used to cause the Zip file to unzip without any user interaction.
Example: d:\sampleapp\codeupdate\SampleApp_Update.exe /auto d:\sampleapp
3. **lcApplicationName**  
The name of the application which is used in display dialogs.

The update file that is downloaded should be a self-extracting ZIP file. I use WinZip and the WinZip self-extractor but any self extractor will work as long as it can extract into a specific directory without user intervention. When you create the Zip file you can include subdirectories as necessary. What I like to do is set up an update directory for my application and copy all the files to update into it, then run WinZip against that to create my Zip file. Check 'Include Subdirectories' and make sure not to check 'Save full path info'. 

After you've created the Zip file you will need to create a self-extracting Zip file and you can use the <a href="http://www.winzip.com/winzipse.htm?wzt" target="top">WinZip Self-Extractor</a> for creating a self-extracting Exe. It's very important that you set up the Exe in such a way that no prompts occur and so you should use all the default settings (simply click Next on each step of the Wizard until you see Finish). Once created the Exe can be extracted by using the /auto option to run without user interface.