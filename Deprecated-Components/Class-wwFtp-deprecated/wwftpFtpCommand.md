This method allows you to execute FTP commands against the server. 

If you want to get a response from the FTP server you have to pass in a buffer parameter that will be able to hold the result from the transfer. The behavior of this function is tied to WinInet.DLLs use of the FTPCommand function. Please refer to the MSDN documentation to see what commands are supported.