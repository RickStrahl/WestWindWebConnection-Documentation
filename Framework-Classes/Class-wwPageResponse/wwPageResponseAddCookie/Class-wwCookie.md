This is a support class for the [wwPageResponse::AddCookie()](VFPS://Topic/_1O80YQ3EY) method to allow setting cookie options more cleanly than the older way of passing a long list of parameters.

To set a cookie in this way use code like the following:

```foxpro
loCookie = CREATEOBJECT("wwCookie")

loCookie.CookieName = "testvalue"
loCookie.Value = "NewValue"
loCookie.Expires = DATE() + 10
loCookie.SameSite = "Strict"
loCookie.Secure = .T.
loCookie.HttpOnly = .T.

* ? loCookie.ToString()  && outputs the generated cookie string

*** Pass the cookie to set on the current request
Response.AddCookie(loCookie)
```

which produces a cookie string that looks like this:

```text
testvalue=NewValue; path=/; SameSite=None; HttpOnly; Secure; Expires=Sun, 10 Nov 2019 10:00:00 GMT
```