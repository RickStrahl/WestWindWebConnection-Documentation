The ExpandScript method method is used to expand script pages that contain FoxPro expressions and Code blocks using Active Server like syntax. The method generates a fully self contained HTML page/HTTP output which includes an HTTP header.

This class uses the wwScripting class to parse <%= %> expression blocks and <% %> codeblocks. ExpandScript() behavior can be called explicitly or is available through generic script processing in the wwScriptMaps process handler which can be mapped for any extension in your server's Process method.

ExpandScript() call syntax Examples:

```foxpro
Response.ExpandScript()                     && Request.PhysicalPath() is used
Response.ExpandScript("~\scriptdemo.wcs")   && Virtual Path as base
Response.ExpandScript(Config.cHtmlPagePath + "scriptDemo.wcs")
```

Example HTML markup:

```html
<div class="customer-list">
    <%
       SELECT * from Customers INTO CURSOR TQuery
       SCAN
    %>
        <div class="customer-item">
            <div class="company-text"><%= TQuery.Company %></div>
            <div class="company-date"><%= TQuery.Entered %></div>
        </div>
        
    <% ENDSCAN %>
</div>
```