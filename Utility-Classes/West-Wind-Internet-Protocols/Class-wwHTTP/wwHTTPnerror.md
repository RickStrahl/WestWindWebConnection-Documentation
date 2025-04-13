Returns the error code from the last request. This value is 0 if no error occurred or non-zero if an error occurred.

This property should be used to definitively determine whether a request succeeded or failed. If it's non-zero the request failed.

The error codes come either from WinInet (12000-13000 range), from the Windows API (anything below that generally 0 - 255), or from HTTP Errors (400-600 typically). 

Most errors will also set the cErrorMsg property which you should check for further info. However some errors do not. The following URL provides more info on WinInet error codes that might not be 'parsed':

<a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/winhttp/http/error_messages.asp" target="top">http://msdn.microsoft.com/library/default.asp?url=/library/en-us/winhttp/http/error_messages.asp</a>