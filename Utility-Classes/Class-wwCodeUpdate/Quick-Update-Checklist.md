To add auto update to your application follow these quick steps:

1.	Create code similar to the code shown in <a href="How wwCodeUpdate works" target="top">How wwCodeUpdate works</a>
to check for updates and download them.

2.	Copy CodeUpdate.vbs or compile and create CodeUpdate.exe into your application
directory and ship it with your application.

3.	Update your application's 'hard' version number that you compare against.
This typically will be a header file constant or a value stored in a file.

4.	Create an update structure on your harddrive that makes up 
all the files that need updating.

5.	Create a Self-Extracting Zip Exe file of your update file(s).

6.	Copy the Self-Extracting file to the Web server in the proper
Web download directory.

7.	Create or update the XML Web Resource file and copy it 
to the Web server server. See Listing 2 for content.

8.	Test the update process preferably separately from the live version.