This step determines whether to copy the Web Connection ISAPI or .NET modules to the virtual folder and allows you to optionally sync settings with the settings from a Web Connection Application INI file (yourapp.ini).

![](IMAGES%2FManagementConsole%2FSyncWcServerSettings.png)

The first checkbox if checked copies the latest version of the Web Connection ISAPI or .NET module to the Web folder. If unchecked the files are not copied and you use what is already there.

The bottom section configures file based messaging by specifying the temp path and file prefix to be used for message files. If you chose to select a server INI file these values are read from that file. These settings are then synced between the server INI files and wc.ini or web.config.


<hr>
Goto [Step 3](vfps://Topic/Step%203%20-%20Create%20Scriptmaps)