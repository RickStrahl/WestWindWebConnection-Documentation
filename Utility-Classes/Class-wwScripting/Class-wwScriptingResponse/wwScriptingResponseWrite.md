The core Write method for the Response object. Use this method to write output from within a script block.

Example:
```foxpro
<%
    lcOutput = ""
    for x = 1 to 5
        lcOutput = lcOutput + TRANS(x) + "<br>"
    endfor
    Response.Write(lcOutput)
%>
```