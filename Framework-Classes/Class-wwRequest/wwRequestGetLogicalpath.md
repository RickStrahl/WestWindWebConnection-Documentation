Returns the web server relative path of the current request. For example:

**http://www.west-wind.com/wconect/Testpage.wc**

returns:

**/wconnect/Testpage.wc**

The path is always in Web format with forward slashes and is relative to the Web server's root directory. 

This function returns the equivalent of `ServerVariables("SCRIPT_NAME")`. When the `llProxiedPaths` is true the `HTTP_X_ORIGINAL_URL` server variable is checked first and if set returns the original URL value on proxy requests.