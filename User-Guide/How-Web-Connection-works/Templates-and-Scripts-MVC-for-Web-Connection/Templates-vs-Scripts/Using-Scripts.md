Web Connection scripts are used for MVC style templating to separate your HTML rendering from your request processing code. Scripts are the **V**iew in M**V**C.

Scripts are standalone templates, that are compiled into real FoxPro program files. The Script template is parsed from into an executable `PRG` file, compiled and executed as an `FXP` file at native FoxPro speed.

Scripts are typically called from:

* `Response.ExpandScript()` in a Process Method
* Directly invoked from a URL via script map (ie. `/wconnect/TestPage.wwd`)

Scripts are HTML templates that contain delimited `<% %>` codeblocks and `<%= %>` or `<%: %>` expressions that are executed and embedded in place in the HTML content. This is similar to the way that ASP or PHP work: The page is rendered top down and the embedded code is executed as the page renders. You can take advantage of all of FoxPro's features in script pages including code blocks that wrap HTML content around FoxPro structure statements like a `SCAN`.

Scripts can optionally take advantage of [Layout and Partial pages](VFPS://Topic/_4DS19CQWR) to separate and 'componentize' the HTML layout into smaller and reusable templates that are easier to maintain. Layout pages allow for creating a 'site layout' that is used on all or most pages and into which page content can be loaded and which removes the 'boiler plate' HTML that is required to set up each page before you start adding your custom content. Partial pages can break out a block of HTML template into separate files to break down page complexity and enable reusability of components on multiple pages. It's highly recommended you at minimum use Layout pages to remove redundant page set HTML.

### How Scripts Work
The parser takes the all static text from the template and turns into FoxPro string expressions that are written as text into an internal `Response` object. This `Response` object is an output stream that eventually produces a string. Any `<% %>` code blocks are directly embedded - as is - into the PRG file as raw code. Any `<%= %>` and `<%: %>` expressions are turned in `Response.Write(EVALUATE(expression))` calls to render as strings into the `Response` output.

To demonstrate here's an overly simple and unstyled example script page:

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

The parser turns this script into a PRG file (truncated):

```foxpro
LOCAL CRLF
CRLF = CHR(13) + CHR(10)
Response.Write([<html>]+ CRLF +;
   [<body style="font:normal normal 10pt Verdana">]+ CRLF +;
   [<h1>Customer Table Display</h1>]+ CRLF +;
   [<hr>])
   
SELECT * FROM TT_CUST INTO Cursor TQuery 
lnReccount = RECCOUNT()

Response.Write([]+ CRLF +;
   [Customer Table has ])

Response.Write(TRANSFORM( EVALUATE([ Reccount() ]) ))

Response.Write([ records.<p>]+ CRLF +;
   []+ CRLF +;
   [<table border=1 style="font:normal normal 8pt Tahoma">])

SCAN

    Response.Write([]+ CRLF +;
       [<tr><td>])
    Response.Write(EncodeHtml(TRANSFORM( EVALUATE([ TQuery.Company ]) )))
    Response.Write([</td><td>])
    Response.Write(EncodeHtml(TRANSFORM( EVALUATE([ TQuery.Address ]) )))
    Response.Write([</td></tr>])
ENDSCAN 
Response.Write([]+ CRLF +;
   [</table>]+ CRLF +;
   []+ CRLF +;
   [</body></html>])
```


Notice that you use `<% %>` for blocks of script code that run like a program, and `<%= %>` or `<%: %>` (HtmlEncoded) to output Evaluated expressions that are concatenated to the string. `<%: %>` should only be used with a single expression while `<% %>` can host multiple commands (translated into TEXTMERGE terms `<%= %>` are TextMerge expressions, while `<% %>` is considered pure code. Anything else (the static HTML) is what goes between `TEXT...ENDTEXT` directives).

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

### Updating Scripts on the Server
Scripts are turned into compiled FoxPro code, so any time a script is changed the next time the script executes it needs to detect that the script has changed and recompile in order to re-execute the code.

#### Overhead
This process has some overhead even if the script has not changed because execution has to check to see if the script has changed by checking file dates.

This can be mitigated by running in **Compiled Script Mode** via the `Server.nScriptMode` property which determines how scripts are executed:

* 1 - Dynamically compiled at runtime
* 2 - Pre-compiled FXP files - no version checks

This value can be set in configuration in `myapp.ini`:

```ini
[Main]
...
ScriptMode=1
```

A stable production application should set this value to `2` but you have to make sure that files have been compiled and preferrably have been compiled on the server by executing pages.

#### Dynamic Compilation on Live Server
Compiled mode is a good idea, but once the flag is set you need to insure that pre-compiled scripts exist on the server. **If the FXP isn't there Compiled mode will fail!**

It's possible to run in Dynamic Mode on a server but there's a caveat: If a script is changed the change may not be reflected in multiple instances of the server. The change will be detected in one instance that sees the change first and it will update and recompile the script. It'll be visible there, but other instances will have the FXP cached in memory and won't detect the change since the FXP will be already newer. To make sure that the all servers are updated you need to restart the FoxPro server instances.

Personally I prefer running in Dynamic mode despite this issue because it allows making changes to scripts and layouts - for critical applications it's just a matter of making sure to restart server instances on the server after updates. There's a little overhead, but on the fast machines we run today the overhead is very small.