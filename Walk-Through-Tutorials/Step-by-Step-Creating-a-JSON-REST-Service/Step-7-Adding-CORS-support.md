One thing that is important when you create  REST API that might be consumed from a domain other than the same domain your are running the API off of, is that browser based JavaScript does not have cross domain HTTP access support by default. This means if you want your JavaScript API to be accessible from *domain b* when your API is running on *domain a*, the API call will fail with a security error.

In order to make this work you need to handle the  <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS" target="top">CORS (HTTP Access Control)</a> protocol on your server. It's a two-way protocol triggered by an `Origin` header in the client's request headers, that requests confirmation of certain request permissions for the originating domain, Http methods and security features of the request.

If you're interested in the details, there's a blog post available here:

* [What is CORS and how to set it up in West Wind Web Connection](https://west-wind.com/wconnect/weblog/ShowEntry.blog?id=57038)

What follows is just the code you can use for a CORS implementation.

### Setting up CORS in a Web Connection Application
CORS is comprised of a number of headers that let you specify who and what can be accessed by your Web application. This is done by providing adding a few custom HTTP headers to your Response output on a request that includes an `Origin` header. CORS requests come in two flavors:

* As an OPTIONS request
* As an in-line request

OPTIONS requests are fired for any non `GET` or `POST` requests, or `GET` and `POST` requests that include Authentication, Cookies or custom headers. Again the post mentioned above has more details on what is triggered and what the exact requests and responses look like.
 

The best place to put this code is in the `OnProcessInit()` method of your process class so it fires on every request:

```foxpro
*********************************************************************
* Function CustomerRestService :: OnProcessInit
************************************
*** If you need to hook up generic functionality that occurs on
*** every hit against this process class , implement this method.
*********************************************************************
FUNCTION OnProcessInit
LOCAL lcOrigin, lcRequestHeaders, lcVerb

*** Unrelated
Response.Encoding = "UTF8"
Request.lUtf8Encoding = .T.

*** Add CORS headers to allow cross-site access on REST calls for browser access
lcOrigin = Request.ServerVariables("HTTP_ORIGIN")

IF !EMPTY(lcOrigin) 
***    *** Optional - validate origin against a domain list (you implement)
***    IF !THIS.CheckForValidOrigin(lcOrigin)
***       Response.Status = "403 Forbidden"
***       RETURN .F.
***    ENDIF 

	*** Allow all domains IP addresses effectively
	Response.AppendHeader("Access-Control-Allow-Origin", lcOrigin)  
	Response.AppendHeader("Access-Control-Allow-Methods","POST, GET, DELETE, PUT, OPTIONS")	
	
	lcRequestHeaders = Request.GetExtraHeader("Access-Control-Request-Headers")
	if EMPTY(lcRequestHeaders)
	  lcRequestHeaders = "Content-Type, Authorization" && ,Cookie if you use cookie auth!
	endif
	Response.AppendHeader("Access-Control-Allow-Headers", lcRequestHeaders)
	Response.AppendHeader("Access-Control-Allow-Credentials","true")
ENDIF

lcVerb = Request.GetHttpVerb()
IF (lcVerb == "OPTIONS")
    Response.Status = "204 No Content"
	RETURN .F.  && Stop Processing
ENDIF

*** ... other OnProcessInit() code
```
CORS requests need to be serviced when an `Origin` header is present in the client's request and you can retrieve this header via `Request.ServerVariables("HTTP_ORIGIN")` or `Request.GetExtraHeader("Origin")`.

The CORS code above allows basically access to all requests because it simply echoes back the requested domain - ie. every domain is allowed. You can optionally add logic that checks for specific domains and if desired fail those by aborting and return a `403 Forbidden`.

CORS requests come in two flavors:

* **Pre-Flight OPTIONS Request**  
This type of request is used for any request that includes authentication, cookies or any custom extra headers and results in a separate HTTP `OPTIONS` request that should return only the HTTP headers, including the validated CORS headers.

* **In-Flight Request**  
Certain `GET` and `POST` requests that don't include authorization, cookies or custom headers request CORS headers inline.

 
`Access-Control-Allow-Methods` and `Access-Control-Allow-Headers` allow you to restrict specific headers that the CORS protocol checks on the client. You can limit which types of verbs are allowed. Many applications allow only `GET` operations and explicitly setting to `GET` prohibits any data sent to the server which reduces security issues if your API is read-only. 

You can also dynamically control these headers based on the incoming requests, but for most applications using these global settings in OnProcessInit() should be sufficient.