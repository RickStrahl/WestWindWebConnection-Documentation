To access the utility click on the Web Connection Menu once you've started Web Connection. If the menu doesn't exist DO WCSTART to get it loaded. Choose Tools|Text Wrapper. You'll see a simple interface that shows you a block of text from the clipboard to be wrapped line by line with enclosing text tags provided at the top of the form.

![](/images/misc/textwrapper.gif)

The above text in the edit window will be converted into a string delimited piece of code that you can easily paste into a code snippet of a PRG/Class:

For example:

```foxpro
lcHTML = ;
[<HTML>]  + CRLF +;
[<HEAD><TITLE>Cookie Test</TITLE></HEAD>]  + CRLF +;
[<BODY BACKGROUND="/wconnect/whitwav.jpg">]  + CRLF +;
[<FONT FACE="Verdana"><H1>Cookie Test</H1></Font><HR>]  + CRLF +;
[This is a test of an HTML document]  + CRLF +;
[</BODY>]  + CRLF +;
[</HTML>]  + CRLF 
```

This can be very useful for quickly capturing HTML and then parameterizing this HTML in your code. For example you may capture a table created with ShowCursor and then grab a single row to optimize performance with manual code operation. Grabbing the HTML gives you a good head start on the layout.

Note that the start and end delimiters are configurable, so this utility can be used for more things than building HTML strings.

> #### @icon-info-circle Use Templates and Scripts
> Although this utility is a great tool to get HTML into code, keep in mind that it may be more flexible to use templates or script to handle display of HTML rather than trying to embed HTML into code. Use code based HTML only if you need either maximum performance or have complicated needs that can't be handled by templates effectively.