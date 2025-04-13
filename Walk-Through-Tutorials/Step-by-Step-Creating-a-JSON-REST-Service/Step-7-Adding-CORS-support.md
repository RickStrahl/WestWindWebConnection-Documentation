One thing that is important when you create  REST API that might be consumed from a domain other than the same domain your are running the API off of, is that browser based JavaScript does not have cross domain HTTP access support by default. This means if you want your JavaScript API to be accessible from *domain b* when your API is running on *domain a*, the API call will fail with a security error.

The workaround for this issue is to set up <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS" target="top">CORS (HTTP Access Control)</a> on your server. Essentially, your server code provides a number of additional headers to allow the application to send data to clients that it permits.


### Setting up CORS in a Web Connection Application
CORS is comprised of a number of headers that let you specify who and what can be accessed by your Web application. This is done by providing adding a few custom HTTP headers to your Response output.

The best place to put this code is in the `OnProcessInit()` method of your process class so it fires on every request:

```foxpro
*********************************************************************
* Function CustomerRestService :: OnProcessInit
************************************
*** If you need to hook up generic functionality that occurs on
*** every hit against this process class , implement this method.
*********************************************************************
FUNCTION OnProcessInit

*** Explicitly specify that pages should encode to UTF-8 
*** Assume all form and query request data is UTF-8
Response.Encoding = "UTF8"
Request.lUtf8Encoding = .T.


*** Add CORS header to allow x-site access from other domains/mobile devices
Response.AppendHeader("Access-Control-Allow-Origin",Request.ServerVariables("HTTP_ORIGIN"))
Response.AppendHeader("Access-Control-Allow-Methods","POST, GET, DELETE, PUT, OPTIONS")
Response.AppendHeader("Access-Control-Allow-Headers","Content-Type,*")
*** Allow cookies and auth headers
Response.AppendHeader("Access-Control-Allow-Credentials","true")

lcVerb = Request.GetHttpVerb()
IF (lcVerb == "OPTIONS")
   *** Just exit with CORS headers set
   *** Required to make CORS work from Mobile devices
   RETURN .F.
ENDIF   

RETURN .T.
ENDFUNC
```

This CORS code allows basically access to all requests.

The `Request.ServerVariables("HTTP_ORIGIN")` essentially allows the domain from which the client is requesting a CORS connection. You can also use `*` for the value which allows all domains, but using the `HTTP_ORIGIN` header value ensures that the client is actually connecting via the CORS protocol and is providing the **HTTP_ORIGIN**.

`Access-Control-Allow-Methods` and `Access-Control-Allow-Headers` allow you to restrict specific headers that the CORS protocol checks on the client. You can limit which types of verbs are allowed. Many applications allow only `GET` operations and explicitly setting to `GET` prohibits any data sent to the server which reduces security issues if your API is read-only. 

You can also dynamically control these headers based on the incoming requests, but for most applications using these global settings in OnProcessInit() should be sufficient.