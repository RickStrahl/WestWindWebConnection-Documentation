The wwSOAP and wwSOAPProxy classes both use an underlying wwHTTP object to manage its HTTP access. By default these objects are auto-configured to common settings which work fine for most generic Web Services, but in some instances you may have to customize the HTTP settings in order to work with specific situations:
<ul>
* Set a customized timeout
* Provide a username and password to access authenticated Web Services
* Use an HTTP Proxy to access the Internet
</ul>

**Timeouts**  
Timeouts are the easiest to handle simply by setting the wwSOAP object's nHTTPConnectTimeout property to x number of seconds.

<pre>oSOAP = CREATE("wwSOAP")
oSOAP.nHTTPConnectTimeout = 60
</pre>

The default is 15 seconds which works fine for most typical transactional Web Services but will be too short for longer operating Web Services.

**Custom wwHTTP Object**  
For full control over the HTTP protocol used with the SOAP client you can create an HTTP object and assign that to the SOAP object. Using this approach you can set request timeout, Proxy settings, authentication and more:

<pre>oHTTP = CREATE("wwHTTP")

* ** Examples of some things you might set - Note not all are required of course
oHTTP.nHTTPConnectTimeout = 5

oHTTP.cUsername = "rstrahl"
oHTTP.cPassword = "whatever"

oHTTP.nHTTPConnectMode = 3  && Proxy
oHTTP.cHTTPProxyName = "proxy-server.hawaii.rr.com"
oHTTP.cHTTPProxyUsername = "rstrahl"
oHTTP.cHTTPProxyPassword = "whatever"

* ** Other custom stuff
oHTTP.nHttpServiceFlags = INTERNET_FLAG_IGNORE_CERT_DATE_INVALID + INTERNET_FLAG_IGNORE_CERT_CN_INVALID

oSOAP = CREATE("wwSOAP")
oSOAP.oHTTP = oHTTP

oSOAP.AddParameter("lcType","ALL")
lcXML = oSOAP.CallWSDLMethod("GetTypes","http://www.foxcentral.net/foxcentral.wsdl")</pre>