This method can be used to reload COM Servers from within your Web Connection application code. The most common scneario for this feature is to refresh configuration file settings.

This method calls the Web Connection Server Release URL which forces servers to shut down and restart and re-reread their startup configuration file settings.

**Requirements:**  
* Must be operating in COM mode
* Server.cCOMReleaseUrl must be set properly and point at the appropriate Release URL
* (http://www.myserver.com/myapp/bin/wc.dll?_maintain~Release)
* You are responsible for providing username and password if security is enabled

This operation fires an asynchronous HTTP request against the ComReleaseUrl. The asynchronous call makes it possible to return a server response from a request that fires it.

Note Username and Password are likely required and your application will have to provide this info somehow in order to allow access to this administrative URL. Some ways to do this is either to store the information somewhere and read it in at runtime, or prompt the user for it as part of the operation that triggers the restart.