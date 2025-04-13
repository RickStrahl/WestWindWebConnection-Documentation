Uploads a file to an FTP site. Works the same as FtpSendFileEx() except it uses a single WinInet PutFile() operation and doesn't provide individual file progress information.

Works better for small files where individual file progress information is not required. This function maybe more reliable than FtpSendFileEx().