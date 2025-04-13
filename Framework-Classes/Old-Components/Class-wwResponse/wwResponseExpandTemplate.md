The ExpandTemplate method is used to expand template pages that contain FoxPro expressions using Active Server like syntax. The method generates a fully self contained HTML page/HTTP output which includes an HTTP header.

The template syntax allowed is simple and looks as follows:

```html
<%= Version() %>
<%= Table.FieldName %>
<%= MyUDF() %>
<%= PrivateVar %>
<%= Class.Property %>
```
or 

```html
<%
   Code Block (any valid procedural FoxPro code)
%>
```

> Note that code blocks are self contained and cannot span multiple block statements. You can't use `<% if .T. %>` and `<%= endif %>` in separate blocks to conditionally include text in between. To use this behavior you can to use [ExpandScript()](VFPS://Topic/_1VO07HGB0).

Expressions are expanded prior to sending the document back to the client, so expressions are valid anywhere inside of the HTML document including in HTML field values and HREF links etc.