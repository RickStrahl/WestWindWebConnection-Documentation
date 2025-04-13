Templates are similar to Scripts using very similar ASP like syntax with `<% %>` and `<%= %>` or `<%: %>` tags. But unlike Scripts which are turned into compiled FoxPro code, templates are **parsed and evaluated at runtime** everytime they are loaded.  Think of Templates like fancy FoxPro `TextMerge` blocks.

It turns out if you're using mainly expressions with `<%= %>` and `<%: %>` syntax this is **very fast** - in most cases faster than the compiled Scripts code. But when you add script blocks to Templates that speed advantage fades fast.

Templates work best for displaying pure 'Views' - objects or values of data that was previous created and formatted in the code preceding the call to ExpandTemplate and you can then embed expressions into the page to display the view:

```html
<div class="item">
    <div><%: TQuery.Company %>
    <div><%: TQuery.CareOf %>
    <hr />
     <div class="errordisplay">
         <%: pcErrorMsg %>
     </div>
</div>
```

Web Connection uses a custom parser to walk through the document to find any `<% %>` blocks and either executes or `EVALUATE()` the embedded code. Expressions use `EVALUATE()` while code blocks use the somewhat slow `EXECSCRIPT()`. 

Each codeblock or expression is evaluated exactly once. 

Expressions are used like this:

```html
<label>Name:</label>
<%: poCustomer.oData.FullName %>
```

Code blocks **must be self-contained** and are executed inline. If you need to render output from the code blocks return a string:

```foxpro
<%
  LOCAL lcOutput
  lcOutput =   "Hello World"
  lcOutput = lcOutput + ", Rick!"
  RETURN lcOutput   && rendered into the output
%>
```

Alternately code blocks can set `PUBLIC` variables that then become available in the template below the code block:

```html
<%
  PUBLIC gcOutput
  gcOutput =   "Hello World"
  gcOutput = lcOutput + ", Rick!"
%>

<label>Your output:</label>
<%: gcOutput %>

<%
   *** Clean up public vars
   RELEASE gcOutput
%>
```


The above works as it's a self contained block of code. Think of the block as an inline, but fully self-contained function that executes and can't directly pass values out of itself.

Unlike scripts **you cannot do things like this in Templates**:

```html
<% SELECT TQuery
   SCAN %>
   Company: <%= Company %>
<% ENDSCAN %>
```

Splitting up structured statements into multiple code blocks is not supported in templates, with the exception of the `IF/ENDIF`.  The following does work:

```html
<% if (plIsActive) %>
    Balance: <% loCustomer.oData.Balance %>
<% endif %>
```

Note that the syntax must include spaces before the if and endif clauses! There's no *else* clause, but you can use multiple if statements to accomplish the same.

> If you find yourself using control blocks and working around them in templates, you should probably consider using Scripts instead.

Templates work best with expressions rather than CodeBlocks. CodeBlocks must be compiled each time, while expressions are simply parsed and evaluated which is very efficient. If you find yourself with a bunch of code in a template, it's usually better to create a function or method in Code somewhere up the callstack and call that function or method from within your template.

You can use any combination of expressions, UDF calls, object properties and methods and any PRIVATE variables that are in scope from the calling code.

```html
<HTML>
<BODY>
<%
PUBLIC oCust
oCust = CREATE("cCustomer")
oCust.Load( VAL(Request.QueryString("PK")) )
%>

Welcome back, <%: oCust.cFirstName %>. Your credit limit is <%= oCust.GetCreditLimit() %>.

<hr>
Time created: <%= TIME() %>

<%
RELEASE oCust
%>
</BODY>
</HTML>
```

Notice that you can use code blocks, but because templates are not self-contained programs any variables that you want to use throughout the page must be declared as PUBLIC (oCust here). However, any regular expression can be accessed as needed.

**Objects available in Templates**  
Templates have the following Web Connection objects available to them:

* Request
* Server
* Process
* Session
* (Response)

Note that the Response object is in parenthesis here! You can use the Response object, but only if and only if you use the `llNoOutput` flag on its various methods. Templates are evaluated into a string and that string is then sent to output all at once after parsing is complete, so using the Response object directly would cause output sent to go before any content in the actual template.

If you need to modify the behavior of the Response object such as changing the HTTP header you need to first clear the object, and then reassign the custom header:

```html
<%
Response.Clear()
Response.ContentTypeHeader("text/xml")
%>
<?xml version="1.0"?>
<docroot>
<test>test value</test>
<%= Response.Write("</docroot>",.T.) %>
```

(note the use of the .T. parameter (`llNoOutput`) on the `Response.Write()` method call! Most response method include this parameter)

If you do this frequently you should consider using Process class methods to handle the header.

```foxpro
Response.AddCookie("TestCookie","Rick")

Response.ExpandTemplate()
```

Scripts like templates can use expressions simply by embedding an expression into the HTML text with <%= %>. Templates should use expressions as much as possible for optimal performance as these are simply evaluated on the fly. You can of course call UDF functions, as well as method of any class that's in scope.