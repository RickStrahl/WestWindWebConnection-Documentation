Adds an HTTP Header to the response.

Examples:
```foxpro
<%
	Response.AppendHeader("Expires","-1")
	Response.AppendHeader("Repeat","5;url=default.wcs")
%>
```