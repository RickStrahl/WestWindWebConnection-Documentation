Adds a Content Type header to a request. Use this method to create a default header or one of the common defaults. For more complex header use the wwHTTPHeader object instead.

<H3>What's a content type?</H3>
Web requests require an HTTP header in order to let the browser know how to display the dynamically generated page. The header looks something like this:
<pre>HTTP/1.0 200 OK
Content-type: text/html

<HTML>
  ...HTML content here.
</HTML>
</pre>	
In the above example the Content Type line plus a blank line is the actual text that makes up the Content type header. 

This method adds this ContentType header to a request. This method is also called internally by various other methods that manipulate the HTTP header. HTMLHeader() and any full page generation methods such as ExpandTemplate(), ExpandScript(), ErrorMsg() and StandardPage() pass forward their lvHeader parameter to this method.