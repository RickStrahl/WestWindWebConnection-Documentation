Returns an HTTP Cookie that was previously set. HTTP cookies allow keeping state by keeping a persistent variable on the user's browser. Cookies are sent along in each HTTP request and appear as a server variable in the incoming request data.

To set a cookie you need to use the [wwHTTPHeader::AddCookie](vfps://Topic/wwHTTPHeader%3A%3AAddCookie) method to assign a cookie as part of the HTTP header for a returned request.

Also see the How To section for [Implementing HTTP Cookies](vfps://Topic/Implementing%20HTTP%20Cookies).