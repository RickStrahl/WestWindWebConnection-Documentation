Web Connection supports two very similar mechanisms for 'scripting'. One uses templates and one uses scripts. The difference between the two are relatively subtle but the key points are:

**Templates**  
*Used with: Response.ExpandTemplate()*

* Are evaluated
* Can be updated as text files and immediately reflect changes
* Can only evaluate FoxPro expressions or CodeBlocks 
* Cannot use nested block code other than `<% if %>` `<% endif %>` blocks
* Are fast to execute, especially if you only use expressions

**Scripts**  
*Used with: Response.ExpandScript()*  

* Are (automatically) compiled as FoxPro code
* Are turned into real PRG files that you can debug and step through
* Support complex logic including expressions, codeblocks and nested control flow blocks
* Are more difficult to update in multi-instance applications due to FXP locking
* Scripts better support Layout and Partial Pages and Sections

In summary, templates work great for expressions. You can use templates with self-contained codeblocks that run as a single block (sort of like a function), but it doesn't work for structured code statements like a `<% SCAN %>` `<% ENDSCAN %>` loop with HTML in between it.

Scripts work with expressions too and the execution path is the same for those, but the strength of scripts is that they are essentially compiled down into a FoxPro PRG file which means you can express anything inside of the `<% %>` brackets into code. All the rest of the document is treated as text strings. You have more control with scripts and scripts are literally executed as a compiled FXP file. You can step through and debug script files by simply putting `<% SET STEP ON %>` into the script. Although Scripts run fully compiled FoxPro code, there's some overhead involved in checking at runtime to ensure that compiled files exist and can execute and unloading the PRG after each execution to allow for hot-swapping the code with updates.

If you're building a full application most likely you'll want to use Scripts to handle the variety of scenarios you need, so that's what I would recommend to use by default. Templates can be a little faster, and if they fit your use case for simple embedded expressions, they can be combined with Scripts. You can easily mix and match. Just make sure you use the right `ExpandScript()` or `ExpandTemplate()` method for the appropriate type of script or template.

### Scripting 101
Scripts are real FoxPro program files parsed from the script's HTML template into an executable `PRG` file. The way it works is that all static text is turned into FoxPro strings that are concatenated into one large string that is returned as a result. Any `<% %>` code blocks are directly embedded into the PRG file as executing code rather than than strings and `<%= %>` and `<%: %>` expressions are concatenated to the output string as `EVAL()` expressions that return strings. Basically the entire page is represented as strings **except** for the code and expression blocks which are embedded into the PRG **as is** and thus execute.

Here's a simple example script page:
```html
<html>
<body style="font:normal normal 10pt Verdana">
<h1>Customer Table Display</h1>
<hr>
<%
SELECT * FROM TT_CUST INTO Cursor TQuery 
lnReccount = RECCOUNT()
%>
Customer Table has <%= Reccount() %> records.<p>

<table border=1 style="font:normal normal 8pt Tahoma">
<%
SCAN
%>
<tr><td><%: TQuery.Company %></td><td><%: TQuery.Address %></td></tr>
<% ENDSCAN %>
</table>

</body></html>
```


To run this page save it to a `.wcs` (Web Connection Script) extension (or your scriptmap extension) and call it via Web URL:

<a href="http://localhost/wconnect/CustomerTable.wcs" target="top">http://localhost/wconnect/CustomerTable.wcs</a>

Notice that you use `<% %>` for blocks of script code that run like a program, and `<%= %>` or `<%: %>` (HtmlEncoded) to output expressions that return a value. `<%: %>` should only be used with a single expression while `<% %>` can host multiple commands (translated into TEXTMERGE terms `<%= %>` are TextMerge expressions, while `<% %>` is considered pure code. Anything else (the static HTML) is what goes between `TEXT...ENDTEXT` directives).

If you have an error in your code the page will bring back an error. For example if I misspell `Reccount()` above as `Recount()` you get a Scripting Error Page that says: 

```
Error:  File 'recount.prg' does not exist. 
Code: Recount() 
Error Number: 1 
Line No: 9 
```

If you're in debug mode (`DEBUGMODE .T.` in `wconnect.h`) Web Connection will also try to find the line of code that caused the error and position the cursor on it. This is not always possible due to the nature of the error, but in most cases and especially those were a simple typo occurred you can edit the change save and re-run the request immediately.

**Fixing up the example**  
Let's change the example around a little more to allow a little more flexibility. Let's fix up the display by adding the `application.css` stylesheet, clean up the table display and add another field (phone) and finally support for asking the user which records he wants to see:

```html
<html>
<head>
<title>Customer Table Scripting Demo</title>
<LINK rel="stylesheet" type="text/css" href="application.css">
</head>
<body>
<h1>Customer Table Display</h1>
<hr>
<%
lcCompany = UPPER(Request.Form("txtCompany"))
lcWhere = ""
IF !EMPTY(lcCompany)
   lcWhere = "WHERE UPPER(company) = lcCompany"
ENDIF


SELECT * FROM TT_CUST &lcWhere ;
   INTO Cursor TQuery ;
   ORDER BY Company
   
lnReccount = RECCOUNT()
%>

<form method="POST" action="customertable.wcs">
Company Filter: <input type="text" name="txtCompany" value="<%: lcCompany %>">
<input type="Submit" value=" Get Customers">
</form>

<hr>
Selected  <b><%= Reccount() %></b> records.<p>

<table border="1">
<tr bgcolor="#EEEEEE"><th>Company</th><th>Address</th><th>Phone</th></tr>
<% SCAN %>
<tr>
   <td  valign="top"><%= TQuery.Company %></td>
    <td><%: IIF(EMPTY(TQuery.Address),[<br>],DisplayMemo(TQuery.Address)) %></td>
    <td><%: IIF(EMPTY(TQuery.Phone),[<br>],DisplayMemo(TQuery.Phone)) %></td>
</tr>
<% ENDSCAN %>
</table>
<hr>
<HR>
</body>
</html>
```

Note that this form has now become both an input and output form at the same time because we're using the form field (txtCompany) in a `<FORM>` tag to ask the user to enter the company filter for the list to create. On each request we then retrieve the user's input using the `Request.Form` method into a variable (which must be `PRIVATE` not `LOCAL` in order to be visible anywhere but in the current `<% %>` block) `lcCompany`. We use the variable to also echo it back in the edit box by using `<%: lcCompany %>` to set the `VALUE=` attribute of the `txtCompany` field. This way the field always displays the user's last choice.

**Script Modes**  
Web Connection's scripting engine can use 2 modes to run scripts:

* **Interactive**  
In interactive mode pages are checked for on disk, file dates are compared and the script if changed is re-compiled and reloaded. Scripts are unloaded when done executing so there's some extra overhead with this mode. Code is still compiled into a PRG and executed as an FXP, but there's overhead in the file comparison and loading and unloading.

* **Compiled**  
As the name implies compiled pages assume that pages are already compiled into `.fxp` files ready to execute. This requires either precompiling or uploading .fxp files to the server as part of your deploy script (Note that this has caveats if your paths are different). Compiled script pages do not check for changes and are not unloaded after execution which makes them faster, but also don't allow for scripts to be updated on the server.

**Objects available in scripts**  
Scripts have the following Web Connection objects available:

* Request
* Response
* Server
* Process
* Session
* Any `PRIVATE` or `PUBLIC` variables that are in scope
* Any of FoxPro global objects (`Application` being the main one)

These objects are the standard Web Connection objects and you can look up their properties and methods separately in the various class references. In addition

For additional examples of how to use scripting see the scripting samples on the <a href="http://localhost/wconnect/default.htm" target="top">Web Connection Demo page</a>.

### Templates 101
Templates are similar to Scripts using very similar ASP like syntax with `<% %>` and `<%= %>` or `<%: %>` tags. The main difference between scripts and templates is that templates are not programs but are evaluated by parsing the template file at runtime and expanding any expressions inside of `<% %>` blocks. Think of Templates like fancy FoxPro `TextMerge` blocks.

Templates work best for displaying 'Views' - objects or values of data that was previous created and formatted in the code preceding the call to ExpandTemplate and you can then embed expressions into the page to display the view:

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

Web Connection uses a set of `MergeText()` functions to walk through the document top to bottom and evaluate each expression or codeblock. Each codeblock or expression is evaluated exactly once. You can embed code blocks like:

```foxpro
<%
  LOCAL lcOutput
  lcOutput =   "Hello World"
  lcOutput = lcOutput + ", Rick!"
  RETURN lcOutput   && rendered into the output
%>
```

The above works as it's a self contained block of code. Think of the block as an inline, but fully self-contained function that executes and can't directly pass values out of itself.

Unlike scripts **you cannot do things like this**:

```html
<% SELECT TQuery
      SCAN %>
   Company: <%= Company %>
<% ENDSCAN %>
```

Splitting up structured statements into multiple code blocks is not supported in templates, with the exception of the IF/ENDIF. The following does work:

```html
<% if (plIsActive) %>
    Balance: <% loCustomer.oData.Balance %>
<% endif %>
```

Note that the syntax must include spaces before the if and endif clauses! There's no *else* clause, but you can use multiple if statements to accomplish the same.

Templates work best with expressions rather than CodeBlocks. CodeBlocks must be compiled each time, while expressions are simply parsed and evaluated which is fairly efficient. If you find yourself with a bunch of code in a template, it's usually better to create a UDF or class method to handle that operation, rather than performing this logic in the template in a code block.

You can use any combination of expressions, UDF calls, object properties and methods and any PRIVATE variables that are in scope from the calling code.

```html
<HTML>
<BODY>
<%
PUBLIC oCust
oCust = CREATE("cCustomer")
oCust.Load( VAL(Request.QueryString("PK")) )
%>

Welcome back, <%= oCust.cFirstName %>. Your credit limit is <%= oCust.GetCreditLimit() %>.

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
* 
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

### How to call scripts and templates
Scripts and templates can be invoked in two ways:

* Directly by using the scripting scriptmaps (.WC or .WCS)
* Using the `Response.ExpandTemplate()` and `Response.ExpandScript()`

If you call scripts directly via scriptmap, the pages must be fully self-contained and provide and handle all inputs and outputs - no method code is involved. 

But you can also run a Web Connection process method first and then from witin it call out to the script page. The advantage of this approach is that you can separate the user interface (the script page) from the business logic (the Process class method). In addition you can set up objects and create private variables that will also be in scope in the script page.

```foxpro
* Simple Process method that calls a template
Function CallTemplate
PRIVATE oCust

oCust = CREATE("cCust")
oCust.Load( VAL(Request.QueryString("PK")) )

... additional processing against customer object


*** Now display the template
Response.ExpandScript()
ENDFUNC
```

The template can now use the oCust object as part of the script as long as oCust was declared as PRIVATE (the default if you don't declare it).

```html
<HTML>
<BODY>

Welcome back <%: oCust.cFirstName %>

Feel free to shop around. Your current credit-limit is: <%= oCust.CalcCreditLimit() %>

</BODY>
</HTML>
```

Notice that any PRIVATE variables you declare will be in scope and can be called from the template or script. Any Classes or UDF functions that are in scope or the call stack also can be called directly.

### Paths for ExpandTemplate and ExpandScript**  
Notice that in the example above I didn't provide a path for the template. By default `ExpandScript()` and `ExpandTemplate()` use the physical path for the script file of the request. If I go to `Customer.wcs` it'll look for that file to execute as a template unless you specify a different path.

You can specify file names in a few ways:

* No path - current Physical Path
* Any absolute OS Path
* A Server Relative path with starting with `~/`

The best choice is using the physical path with no parameter. If that's not possible the next best choice is to use 
the `~/` path as it avoids having to hard code a path into your application. Instead it'll figure out where your Web folder is and start looking from the root there into the path you provide following the `~/`.

```foxpro
Response.ExpandScript("~/Admin/DataAdministration.wwd")
```

The `~/` path is resolved:

* From IIS request data which provides a root path
* If that's not there because your Web Server may not provide it,
  using `cHtmlPagePath` in the Process class Configuration in `yourapp.ini`

The latter is defined in `yourapp.ini`. For the demo it's `wwdemo.ini` for example:

```ini
[wwDemo]
datapath=wwDemo\
htmlpagepath=d:\inetpub\wwwroot\wconnect\
```

This setting is accessible inside of a Process class as `Config.cHtmlPagePath` and if IIS on a request does not return a physical application path, automatically used to resolve the `~/` path.

The Web Connection Handler and Middleware both return the ApplicationPhysicalPath as part of the request data and you can grab this value via [Request.GetApplicationPath()](VFPS://Topic/_1C10X7B3S).