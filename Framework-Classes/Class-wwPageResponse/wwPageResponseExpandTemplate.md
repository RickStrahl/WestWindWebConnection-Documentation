The ExpandTemplate method is used to expand template pages that contain FoxPro expressions using Active Server like syntax using `< % expression() % >` and `< % Command % >` syntax inside of HTML documents. The method generates a fully self contained HTML page/HTTP output which includes an HTTP header.

ExpandTemplate can expand files from disk based on a path or from a string.

**FoxPro Expressions:**

```html
<b><%= Version() %></b>
<hr/>
<%= Table.FieldName %>
<hr/>
<%= MyUDF() %>
<hr/>
<%= PrivateVar %>
<hr/>
<%= Class.Property %></pre>
```

**Script Blocks:**

```html
<label>Numbers entered:</label>

<div class="result">
<%
lcOutput = ""
FOR x = 1 to 10
   lcOutput = lcOutput + TRANS(x) + " "
ENDFOR
Response.Write(x)
%>
</div>
```

Expressions are evaluated in sequence from top to bottom of the document.

> #### @icon-warning Script Blocks are self contained Procedures
>Please be aware that script blocks (rather inside of templates are executed as self-contained procedures, meaning any variables you declare inside of the script block won't be visible outside of the script block. You also can't use structure statements that wrap HTML or other expressions.
>
>Keep script blocks to a minimum, they are expensive to process while expressions are fast. If you need lots of script blocks or need more sophisticated structured commands you can use [wwPageResponse::ExpandScript()](VFPS://Topic/_1O80YQ3IK) instead which offers scripting converted to full blown FoxPro PRG files.