The wwCodeupdate class is fairly self contained, but there are a couple of dependencies that you need to consider.

**An Internet Connection**  
Obviously you need an Internet connection to check for the Web version resource and download the update file. If no connection is available the checks will simply fail quickly and not affect your application other than a short delay. Timeouts will determine how long the app waits in the worst case scenario where there is a network connection, but no Internet connection. Use nConnectTimeout to set the length of time waited before timing out.

**MS Common Dialogs Active X control**  
MSCOMCTL.OCX is required for the progress bar dialog and should be installed with your application. Most desktop applications use these controls anyway.

**Windows Scripting Host**  
This software should be installed on all current versions of Windows starting with Windows 98SE and later and any machine that has IE 5.0 or later installed. Generally you won't have to worry about this, but to be safe you might want to require your application to have IE 5.0 installed. You can check from within your application with whether the Scripting Host is installed by using IsComObject("Wscript.Shell"). IsComObject() is contained in wwUtils which is included in the source code. You can avoid this dependency by creating an Exe file of your own - a VFP Exe or if you don't want any dependencies a C++ or Delphi Exe.

**A Zip Self-Extractor or the like**  
The wwCodeUpdate class assumes that you have a self-extracting ZIP file that contains the update. Specifically it relies on WinZip's Self-Extractor, but you can change the command line to fit any other self-extractor in the SwapExe() method of the wwCodeUpdateClass. You can get WinZip from www.winzip.com. The advantage of using a ZIP file is that you can store multiple files and actual directory structures inside of the file.