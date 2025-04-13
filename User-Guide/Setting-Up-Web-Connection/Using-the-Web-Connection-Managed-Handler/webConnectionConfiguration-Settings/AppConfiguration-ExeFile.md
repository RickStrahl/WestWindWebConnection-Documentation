Name of the EXE file for the server application. 

Used for hot swapping functionality and killing the servers when shutting down. The module also uses the file name to retrieve version information as well for loading file based instances. It's fairly crucial that this value is set correctly.

The exe file needs to be a fully qualified path name or using a ~\ path that is relative to the Web root folder.

Example of a ~\ Path:
~\webconnection.deploy\sidebysideserver.exe

where ~\ is replaced with the Web Root folder like c:\inetput\wwwroot\sidebyside\.