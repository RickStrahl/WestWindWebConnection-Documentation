***Providing all HTTP output to your HTTP application. This class abstracts the output generation for the various messaging mechanisms (File, COM, ASP) and provides full control over all aspects of HTTP generation.***

The Response object is very flexible in that it is designed to handle multiple different output sources via special subclasses that implement only a few methods to handle the physical output generation logistics. Web Connection implements the following:

```
wwResponse
     wwResponseFile
     wwResponseString
     wwASPResponse
```

The lower classes implement only a select few low level methods (Write, FastSend etc.), while the core functionality is implemented at the wwResponse level. The Web Connection framework determines which of these subclasses of the wwResponse object to implement based on the request mode of the current request. 

### Response Lifetime
The Response object is created whenever a wwProcess object is created, which occurs on every hit. Web Connection calls into your custom process class and passes in the Request object as a startup parameter. The request object provides the context required to determine which mechanism (File, String, ASP) is in use and based on that wwProcess creates an instance of the wwResponseXXXX object.

This newly created object becomes your main output mechanism and is passed to your code as a surrogate object of the wwProcess class as wwProcess::oResponse, or simply as a PRIVATE variable called **Response**(if you use the default processing of the Process method).

### Functionality
The Response object is very powerful in the features it provides your application with. At the lowest level it provides the Write() method which is used for all output travelling back to the Web server. Write is the low level that all other methods in the wwResponse object call to get output sent to the server. These other methods simply create more complex HTTP output and eventually call Write() to submit the output into the HTTP output stream.

Write and its slightly more efficient FastSend companion method are the low level functions, but wwResponse also provides a number of high level features:
* ExpandTemplate and ExpandScript can evaluate scripted HTML pages containing Visual FoxPro code and expressions.
* ShowCursor can render a VFP cursor/table as an HTML table with a single line of code
* Various FormXXXX methods can generate HTML input fields with a couple of parameter values.
* Redirect allows you to send an HTTP request to another page.
* Authenticate lets you bring up the browser's login dialog.
* ContentTypeHeader lets you configure and manage the HTTP header directly.
* StandardPage lets you generate a self-contained HTML page with one line of code.

<h3>HTTP Headers</h3>
Every response should have an HTTP header attached to it. By default the Response object creates a standard HTML HTTP header using a content type of text/html which is generated through Response.ContentTypeHeader(), which is implicitly called from many methods such as HTMLHeader, HTMLHeaderEx, ExpandTemplate and ExpandScript. To create headers manually use code like the following:
<pre>lcXML = <somecode that generates XML)
Response.ContentTypeHeader("text/xml")
Response.Write( lcXML)
</pre>

Headers are used for custom HTTP behaviors such as content expiration, content description, cookie, security and much more. Custom headers require that you use the wwHTTPHeader object to create the header. You can then either output this header directly or pass it to one of the highlevel methods as a parameter. This example manually creates output:

<pre>oHeader = CREATE('wwHTTPHeader")
oHeader.DefaultHeader()
oHeader.AddCookie("Name","Rick")
oHeader.AddForceReload() && ExpireContent immediately

Response.Write( oHeader.GetOutput() )  && Write the header into the Response stream
Response.Write( ... content here... )</pre>

If you use one of the high level methods you'd pass the oHeader object as a parameter:

<pre>Response.HTMLHeader("Hello World","Hello World",,oHeader)

SELECT * FROM TT_Cust into cursor TQuery
Response.ShowCursor()
</pre>

For more info see the wwHTTPheader documentation.

<H3>The llNoOutput parameter</H3>
The Write method is implemented like this:

<pre>wwResponse::Write(lcText,llNoOutput)</pre>

The first parameter is the text to send. The second parameter is very important, but optional and allows you to specify whether the text is sent into the HTTP output stream or returned to you as a string! Most other wwResponse object methods also include this same parameter, which allows most methods to be used as string generators rather than outputting to the HTTP stream. This also allows sophisticated nesting of method calls which would otherwise not be possible - a compound method would collect multiple HTML strings into a single string before actually sending the output to the HTTP stream. 

You'll see the llNoOutput parameter in most of the wwResponse methods and the behavior is the same for all of them: If you pass it in as .T. the result is returned to you as a string and the input is not actually sent to the HTTP output source.

<H3>SubClassing</H3>
You'll undoubtably will want to subclass the wwResponse class. Note that you can also subclass the lower level methods, but it's not recommended that you do this since those are considered system classes.

When you subclass wwResponse with your own class you need to make some additional configuration settings to make sure the wwResponseFile/String/ASP classes - which are the ultimately implemented classes that the framework uses - know to use your new subclass. To do this you have to make a change in WCONNECT.H. All the Web Connection classes are created using DEFINE's that identify each class. For the wwResponse class the line is:

<pre>#DEFINE WWC_RESPONSE			wwResponse</pre>

and you need to replace the wwResponse value with the name of your subclass. 

<pre>#DEFINE WWC_RESPONSE			wwCustomResponse</pre>

Once you do this, make sure you recompile your project or all PRG/VCX files. Once you do the wwResponseFile/String/ASP classes will now use your subclass.