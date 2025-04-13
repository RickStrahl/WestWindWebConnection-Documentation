This property is used to force the HTMLHeader method to use the specified Cascading Style Sheet. Property asks for a URL that points at the CSS file.

There are two ways you can use this method:

* **At the per Request Level**  
You can simply use the the Response object in each of your methods that call the HTMLHeader method set this property for each request that requires a style sheet override.

* **At the Process Level**  
You can have every request in your application use the stylesheet by adding the following into your Process method of your wwProcess subclass:
```foxpro
Response = THIS.oResponse
Response.cStyleSheet = "westwind.css"
```

Since the Process method is the central entry point for each request it will apply to all methods in this class that use HTMLHeader().

It should be noted that currently only the HTMLHeader() method generates a stylesheet automatically. The HTMLHeaderEx method requries that you manually provide the Stylesheet. You can pull the default by querying Response.cStylesheet and pass it to the wwHTMLHeader::AddStyleSheet method.