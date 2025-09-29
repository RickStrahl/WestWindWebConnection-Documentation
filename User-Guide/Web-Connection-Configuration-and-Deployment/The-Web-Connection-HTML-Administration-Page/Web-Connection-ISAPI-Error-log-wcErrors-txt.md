Web Connection is an ISAPI DLL that runs inside of IIS. Since it's a system level component errors don't show on the console, so all errors are logged into a file that you can examine. The file is called wcErrors.txt and goes into the Web Connection temporary file path. Note IUSR_ must have Full Access in this directory in order to create the log and write to it.

The maintenance page contains a Show DLL Errors link you can use to view the log. 

There are a number of errors that are generated in this log that are not critical - they're reported for your information and debugging purposes. Harmless errors include:

**All servers are busy**  
If your server pool is too busy to take further requests in the allocated timeout period you'll see this error message. This is not really an error, but merely a notification that your server is maxed out.

**Forced Release of Server**  
If you're running Automation servers are unloaded by releasing the COM references which release the object when the reference count goes to 0. In some instances related to the multithreaded nature that Web Connection objects are called from the reference counts alone do not allow the objects to unload. In these cases the Web Connection DLL forces the objects to unload by terminating the process explicitly. This is normal for servers that have been running for long periods of time, especially if you use the KeepAlive flag on your server. This information is logged merely for informational purposes.

**Web Connection Request timed out**  
This means that a request took longer than the allotted timeout period as specified in wc.ini. This either means you had a very slow request or possibly your server hung on an error or some unexpected UI operation. If the Web server is busy the offending server was probably unloaded automatically and reloaded. This error is also common when working with file based operation and testing development installations. Anytime you hit a WC link and don't have the server running one of these messages will eventually be generated when the request times out.

**GetIDsOfNames or QueryInterface Failed due to thread Time Out**  
Web Connection Server has been unloaded (RPC Server Unavailable)
These errors relate to crashed or killed Web Connection servers that unloaded while they were idle. When Web Connection tries to access these server these errors occur. These errors are actually trapped and the server are automatically reloaded at that time so that the client never sees an error in most cases.

The errors above are all benign and are nothing to worry about even if you see a lot of them. There are also permission errors related to maintenance tasks which also are not crucial. Errors to watch out for are:

**Exception Errors**  
Unhandled exception errors are caused by system errors (like heap corruption, corrupted memory) and believe it or not code errors. If you have isolated exception errors that occur very rarely it's nothing to be worried about - IIS has been known to get unstable and memory/COM subsystem errors are something that will occur from time to time. In my experience these errors are very few indeed and most don't manifest themselves as serious to require even a Web server shutdown/restart.

If you run into consistent exception errors make a note of the error message (the nature of exceptions makes this necessarily vague but some will report a little info) and try to get as much context as you can to report the issue. What is the server doing, what types of requests are you running when the problems occurred etc.