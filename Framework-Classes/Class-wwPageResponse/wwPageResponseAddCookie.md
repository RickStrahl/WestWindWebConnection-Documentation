Adds a cookie to the current Response headers.

Cookies are used to store small pieces of persistent information for the server to remember users by. Typically these values should be kept very small and hold only key values to look up additional information about a user. Cookie space is limited in browsers and all applications running on a given site share the cookie space, so don't abuse use of it.

For more info on cookies and values you can look at the <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie" target="top">MDN topic on Set-Cookie</a>.

#### Setting Simple Cookies without Options
```foxpro
Response.AddCookie("cookieName","CookieValue")
```

There are additional parameters that allow you set expiration and cookie options, but if you need to set extra optoins we recommend you use the more comprehensive [wwCookie](VFPS://Topic/_5M50NOCS1) parameter to set the options you need.

#### Using wwCookie to set complex cookies
The recommended way to add a cookie is:

```foxpro
loCookie = CREATEOBJECT("wwCookie")
loCookie.CookieName = "testvalue"
loCookie.Value = "NewValue"
loCookie.Expires = DATE() + 10
loCookie.SameSite = "Strict"   && Strict / Lax or "" for 'default' which is `None`
loCookie.Secure = .T.          && Https only! 
loCookie.HttpOnly = .T.        && Http only can't be read in script code - defaults to .T.

* lcCookieVal = loCookie.ToString()  && returns the generated cookie text

Response.AddCookie(loCookie)   && add the Cookie to the current Response
```

Note that many of the properties are optional - only `CookieName` and `Value` are required. For more information on the values to set see that [wwCookie](VFPS://Topic/_5M50NOCS1) class.

> You can also pass explicit parameters to the method that map the object properties. The cookie is eventually stored as an object in the Response.Cookies collection.