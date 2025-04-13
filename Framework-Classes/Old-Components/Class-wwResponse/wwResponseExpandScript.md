The ExpandScript method method is used to expand script pages that contain FoxPro expressions and Code blocks using Active Server like syntax. The method generates a fully self contained HTML page/HTTP output which includes an HTTP header.

This class uses the wwScripting class to parse <%= %> expression blocks and <% %> codeblocks. 

Expression Examples:

```html
<%= DateTime() %>
<%= lcVar %>
<%= TQuery.FieldName %>
<%= MyFunction() %>
```

Code Block Examples:

```html
<%
    lcOutput = ""
    for x = 1 to 5
        lcOutput = lcOutput + TRANS(x) + "<br>"
    endfor
    Response.Write(lcOutput)
%>
<%
    SELECT Company from TT_CUST INTO CURSOR TQuery 
    SCAN
%>
Company: <%= Company %><br />
<% ENDSCAN %>
```

ExpandScript() behavior can be called explicitly or is available through generic script processing in the wwScriptMaps process handler which can be mapped for any extension in your server's Process method.