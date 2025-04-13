Web Connection provides built-in support support for setting and retrieving HTTP Cookies via two methods in separate objects:

* **[wwPageResponse::AddCookie](VFPS://Topic/_1O80YQ3EY)**  
This method allows you to create a cookie, which means it's sent as part of the HTTP Response header back to the browser. The browser retrieves the HTTP Header and creates the Cookie on the client side. If the cookie is persistent with an expiration date in the future the cookie is written to a file on disk as well as maintained in memory. On every successive hit to this server the client sends the Cookie to the server which can then be read with Request.GetCookie().

* **[wwRequest::GetCookie](vfps://Topic/wwRequest%3A%3AGetCookie)**  
This method is used to read the Cookie retrieved from the browser. The Cookie will be available on every hit until the Cookie either expires (ie. the browser was shut down or the Cookies expiration date is exceeded). Cookies are valid only on the server and virtual directory they were created for and are passed back only within that context.

### Creating the Cookie
To create a cookie with Web Connection you have to use the wwHTTPHeader object:

```foxpro
lcID = SYS(3)  && or whatever value you want to store.

Response.AddCookie("wwuserid",lcID,"/wconnect")

*** HTML document here:
Response.Write("<HTML>Hidi ho</HTML>")
```

The key thing is the `AddCookie()` method which adds the Set-Cookie string to the browser. Not that you can create multiple cookies by making several calls to Addheader. It's best to not create more than one cookie per site - use data stored in tables about the user to retrieve and store any additional data. In short, minimize the data stored in cookies to avoid user apprehension.

### Reading a Cookie
To read a Cookie you simply use the `wwRequest::GetCookie()` method and pass the name of the cookie. Keep in mind that the cookie must have been created on the client first. In other words, a cookie cannot be created and read in the same pass. The Cookie must first reach the client, then can be read on any successive requests. The cookie must also be in scope with the appropriate path on the Web server as described above. If you create a cookie for use in the /wconnect directory `GetCookie()` can only retrieve that cookie when the request afile:///C:/Users/rstrahl/Documents/Html%20Help%20Builder%20Projects/webconnection/checking%20whether%20cookies%20are%20enabled%20in%20browser.htmctually fired in the /wconnect directory either via a request against wc.dll or a script page in that path or downward.

Typical operation on the server side usually involves checking for a cookie and writing the cookie only if the cookie doesn't already exist. Basically, it's a write once, read many situation:

```foxpro
*** Try to retrieve the cookie...
lcId=Request.GetCookie("WWUSERID")

*** If not Found create the cookie
IF EMPTY(lcId)
   *** Create the cookie
   lcId=SYS(3)
   
   Response.AddCookie("WWUSERID",lcId,"/wconnect")
   
   *** To specify a permanent cookie supply NEVER or a specific expiration date
   *** Response.AddCookie("WWUSERID",lcId,"/","NEVER")ENDIF            
ENDIF

... more HTML generation here
```

Note that the cookie is created only once (although you could create a new one on each hit - bad form though), when lcID is blank. Thus when the cookie already exists the IF block is bypassed and no cookie is written. Remember, once the cookie has been created on the browser, there's no reason to re-write it because the value comes down on every request until the browser shuts down or the Cookie expires.

### What does a Cookie look like?
An HTTP Cookie is nothing more than an HTTP header fragment that is sent back from the server to the browser. A header containing a Cookie looks like this:

```
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: WWUSERID=43491556; path=/wconnect
```

The Set-Cookie command instructs the browser to create the client side cookie. A Cookie can contain path information (in this case /wconnect) and expiration information which in this case is omitted. If the expiration is in the future the cookie is persisted to disk and can persist past a browser shut down. If like above, no expiration is provided the Cookie is considered session specific and goes away when the browser shuts down.


### Creating a permanent Cookie
To create a permanent Cookie you have to specify a date in the future. The easiest way to to do this is with:

```
Response.AddCookie("wwuserid",lcID,"/wconnect","NEVER")
```

NEVER in this case is translated automatically into a date in the far future. You can also specify a specific date instead of never, but it must be a real date and it must follow GMT naming. For example:

**Sun, 27-Dec-2009 01:01:01 GMT**  

> Note:
>Dates in the far future beyond this date may cause problems on some browsers. This date works on all tested browsers.</blockquote>

### Cookie Paths
Cookies are specific to the paths that they are created for. Above I set the cookie to be valid only in the /wconnect virtual directory and down. If you don't specify a path / or the root directory will be used by default. By using the root the Cookie is visible on the entire site.

Why deal with specific paths? Some sites use lots of cookies and cookie strings are by spec limited to 256 character lengths although both IE and Netscape implement 1k strings or more. By partitioning Cookies into their virtuals you're avoiding overload of cookies and only retrieve the data you need in the appropriate location.

### Deleting Persisted Cookies
There's no special command to delete a cookie. Instead to delete a cookie you simply have to re-create the cookie and not assign an expiration date or an expiration date in the past.

```
Response.AddCookie("wwuserid","","/wconnect",Date() -1)
```

### Where should I put my Cookie Code?
Placement of cookie check/creation code should be central, since most likely it has to apply on every hit of the application or sub-applet. Hence the best place for this is in your wwProcess::Process subclass, so that this type of check can be performed on every hit to your application. What I typically do is check the Cookie in the Process method and then call a validation method that does whatever it needs to to create the Cookie and validate the user. For e-Commerce sites this is typically something like wwMyApp::HomePage.

```foxpro
FUNCTION Process

lcID = THIS.oRequest.GetCookie("wwuserid")

*** No Cookie - user must go to homepage/login page
IF EMPTY(lcID)
   THIS.HomePage(lcID)
   RETURN
ENDIF

DoDefault()

ENDFUNC


FUNCTION HomePage
LPARAMETER lcID

IF EMPTY(lcID)
  lcID = THIS.oRequest.GetCookie("wwuserid")
ENDIF

IF EMPTY(lcID)
   *** Create the cookie
   lcId=SYS(2015)
   Response.AddCookie("WWUSERID",lcId,"/wconnect")
ENDIF

*** Show HomePage from template 
THIS.ExpandTemplate()
ENDFUNC
```