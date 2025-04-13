Retrieves a server variable from the request data. Server variables provide information about the current request including info about the client application (browser, IP Address), the current request (server name, querystring), authentication (username, port accessing this app) and status information (Cookies, type of request) etc.

Most of the server variables that are returned are abstracted in method of this class, such as GetBrowser(), GetIPAddress(), GetAuthenticatedUsername() and so on.

To see a list of available raw server variables you can capture the current request output by bringing up the [Web Connection Status form](vfps://Topic/The%20server%20status%20form) and saving request data for review.

The following shows typical contents of the ServerVariables available:

<pre>DLLVersion=Web Connection 3.32 (32 servers)
wcConfig=d:\westwind\wconnect\wc.INI
REQUEST_METHOD=GET
PATH_INFO=/wconnect/slowhit.wcs
PATH_TRANSLATED=d:\westwind\wconnect\slowhit.wcs
SCRIPT_NAME=/wconnect/slowhit.wcs
PHYSICAL_PATH=d:\westwind\wconnect\slowhit.wcs
SERVER_PROTOCOL=HTTP/1.1
SERVER_SOFTWARE=Microsoft-IIS/4.0
SERVER_NAME=localhost
SERVER_PORT=80
REMOTE_HOST=127.0.0.1
REMOTE_ADDR=127.0.0.1
AUTH_TYPE=Basic
REMOTE_USER=rstrahl
HTTP_AUTHORIZATION=Basic cnN0cmFobDpkc2ZhZG1z
LOGON_USER=rstrahl
HTTP_USER_AGENT=Mozilla/4.0 (compatible; MSIE 5.0; Windows NT; DigExt)
HTTP_COOKIE=WWTHREADID=880U8OGY; ASPSESSIONIDGGGQQGCB=JNEHCPDAMLKOCNMEPLPNIBEH
GMT_OFFSET=-36000
ALL_HTTP=HTTP_ACCEPT:*/*HTTP_ACCEPT_LANGUAGE:en-usHTTP_CONNECTION:Keep-AliveHTTP_HOST:localhostHTTP_USER_AGENT:Mozilla/4.0 (compatible; MSIE 5.0; Windows NT; DigExt)HTTP_COOKIE:WWTHREADID=880U8OGY; ASPSESSIONIDGGGQQGCB=JNEHCPDAMLKOCNMEPLPNIBEHHTTP_AUTHORIZATION:Basic cnN0cmFobDpkc2ZhZG1zHTTP_ACCEPT_ENCODING:gzip, deflate</pre>

<blockquote>
Note: PHYSICAL_PATH, DLLVersion and wcConfig are specific to Web Connection. 
</blockquote>

Note that Web Connection pulls specific keys out of the HTTP request buffer so not all HTTP variables are necessarily available. Web Connection pulls the most common ones that you are most likely to require for your applications. However, later versions of IIS or special extensions running on the Web Server might add additional HTTP variables to the Web Server. You can access these by telling wc.dll to specifically pull these values for each request by using the wc.ini [Extra Server Variables] section. For more info see the [wc.ini topic](vfps://Topic/The%20wc.ini%20configuration%20file).