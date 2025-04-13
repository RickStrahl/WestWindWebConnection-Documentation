HTTP Redirection allows you redirect the current request to another URL. A common scenario is for login operations where the user is trying to access functionality before he's logged in. When the request comes in you can redirect the user to the Login page first.

Redirection is an HTTP feature that works through the HTTP header and requires that all existing output be discarded first. 

Redirection is also available through the [wwHTTPHeader::Redirect](vfps://Topic/wwHTTPHeader%3A%3ARedirect) method, which this method calls internally to generate the Redirect header.