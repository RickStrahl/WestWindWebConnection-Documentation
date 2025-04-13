The Web Connection ISAPI DLL creates a unique request ID for every request that is fired against the DLL. This ID is used for logging and is also passed to your Web Connection FoxPro Server via a REQUESTID ServerVariable which you can retrieve like this:

<pre>lcID = Request.ServerVariables("REQUESTID")</pre>

You can use this ID to track requests through the entire Web Connection processing cycle. The DLL uses this id for all logging purposes and you can write out the request ID as part of any custom logging your application does.

Web Connection also uses this ID to safeguard any possible corruption in requests and provide request tracking for logging purposes from your own application and the ISAPI extension. To enable this checking (which creates a tiny bit of overhead) set the following key in wc.ini:

<pre>ValidateRequestId=1</pre>

The ISAPI Dll sends the ID to your server, which then returns a RequestId header as part of the response. The ISAPI DLL then checks for the header, extracts the ID and validates it against the original ID generated for the request before the output is sent back to the client. If the IDs don't match - and this should never happen - the response is not sent to the client and an error message is displayed instead. This guarantees beyond any possible doubt that there might be any possible request mixups.

### Why this ID
There have been a few instances in the past where customers have had problems with their hardware or their server configuration that has caused problems with requests returning output for the wrong request to a different user. In the history of IIS there were several known bugs in IIS 3 and IIS 5 that would cause COM server corruption on rare occasions that have resulted in COM servers returning output to another IIS connection. This situation is very, very rare and has never been tracked to Web Connection itself, but when this situation arises there's usually a lot of finger pointing, and having extra request ID tracking can lay to rest any fears that the wrong data is returned from your FoxPro Web Connection Server implementation. 

To ensure our customers of the security of the Web Connection framework, Web Connection uses an explicit check for the Request ID after the result has been returned from the FoxPro server to ensure that the request integrety is fully intact. If the *RequestId* HTTP header is returned from the Fox server this header is checked and compared against the original request id to ensure the ids match. If for whatever reason they don't an error page is displayed instead of displaying potentially incorrect data.

Starting with Web Connection 5.10 the header is automatically added with any request that uses the wwPageResponse class or the wwHTTPHeader class for explicit or implicit headers with earlier Request class versions if the ValidateRequest key is enabled.

On failure an error message will display. On IIS 6 or later if the problem should occur Web Connection will shut down the worker process and restart it automatically as the cause of this sort of mismatch is almost certainly due to COM object corruption which cannot be resolved without a restart.