The wwRequest object is basically your input for a Web request. With it you can retrieve all the information that the Web Server makes available for each request. 

It allows access to:

* **Form Variables**  
Form variables are any input POSTed from the client to the server. Most commonly this is HTML form variables entered onto an HTML form, but it can also be data posted from a fat client application including XML data, or even binary data uploaded to your application. Any POST data can be access. Request.cFormvars

* **QueryString keys**  
Querystring values typically are used as URL parameters to parameterize a request and tell it how to operation. Anything following a question mark is considered the querystring and typically this content is in key value pair format with ampersands separating the pairs:

```
MyPage.wcs?Id=01222S&Action=Edit&Page=1. Request.QueryString("id") retrieves the Id for example.
```

* **ServerVariables**  
Server variables provide information about the current request, the client and the Web server. Think of it as status information. You can get things like Client IP Address, domain name of the server, the port the request runs on, the browser and authenticated user name just to mention a few things. Many of the common access features are exposed with specialty methods.

* **Cookies**  
Cookies are persistent morsels of information that can be persisted across requests and are generally used to track a user as he moves through a site. Because HTTP is stateless Cookies are useful to make it possible to preserve some state - or rather a token to the state in a cookie. Cookies are typically used to store an ID that identifies a user or a Session instance that stores the actual data to persist.
Here's how the access to the various items works:

**Form Variables:**  
```foxpro
lcName = Request.Form("txtName")
```

**Query Strings:**  
```foxpro
lcId = Request.QueryString("id")
```

**Server Variables:**  
```foxpro
lcServerName = Request.ServerVariables("SERVER_NAME")
```

**Cookies:**  
```foxpro
lcCookie = Request.GetCookie("WebStoreCookie")
```

There are many methods that retrieve common Server Variables with simpler names such as `GetBrowser()`, `GetIpAddress()`, `GetExtraHeader()` etc.

The Request class is always available in Web Connection Process classes as **this.oRequest** (where this is the Process class) or more simply just as a PRIVATE variable called **Request**.

The request object provides Form variables and  Server variables. Where do they come from and what do they look like? Well, Web Connection lets you spy at the raw request data in the [Server Status Form](vfps://Topic/The%20server%20status%20form) by capturing request data for a request. See the previous link for details on how to review both request inputs and outputs.

<H2>How it works</H2>
The request object is originally created when wwServer::Init fires. Based on the current messaging mechanism wwServer creates an oRequest and calls its by calling the [wwRequest::InitializeRequest](vfps://Topic/wwrequest%3A%3Ainitializerequest) method. This method retrieves all the request information as a string and assigns it to the oRequest object splitting into cFormVars and cServerVars. 

As the request runs through the WWWC framework a new wwProcess object (your subclass thereof) is created and the oRequest member is set. During the Process method which kicks off the request processing oRequest is assigned to a PRIVATE Request variable which is then available to all Process code - your user code included. 

All your code has to do in a Process method is to access the Request object and its methods. So for example to check for an authenticated user you'd just call Request.GetAuthenticatedUser(). It's just there and ready and waiting for you.