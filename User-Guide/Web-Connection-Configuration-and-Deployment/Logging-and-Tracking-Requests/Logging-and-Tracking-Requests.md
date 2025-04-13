Web Connection supports two kinds of request Logging:

* **[Web Module Logging](vfps://Topic/_1Q31F6NYE)**  
.NET and ISAPI Handlers both support logging of error messages in `wcErrors.txt`. Logging is optional and must be enabled explicitly with the LogDetail=1 flag. When set Web Connection logs every request as it starts and then again as it ends and writes out the entries into wcErrors.txt. The logging writes out the unique Request ID plus the URL. In addition, any errors and messages that occur in the ISAPI processing are also logged into wcErrors.txt.

* **[FoxPro Server Logging](vfps://Topic/_1Q40SZXAA)**  
From within FoxPro and your application Web Connection logs requests to a DBF file. This is also optional but enabled by default. Logging occurs only at the end of a request. This option is controlled by the lLogRequests flag in <yourapp>.ini file which maps to wwServer::lLogRequest and wwServer::cLogFile. 

Both mechanisms log a unique [RequestId](vfps://Topic/_1Q4017NME) which identifies each request.