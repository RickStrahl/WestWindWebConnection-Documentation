MergeText is a template expansion method that can be used to 'merge' text into a document. The most common use you may be familiar with is for Web scripting where Active Server Style tags can be used to embed FoxPro code and expressions into an output stream.

Template expansion evaluates a page top to bottom. It does not 'run' the page. Rather each markup tag is evaluated or executed and then embedded into the output stream.

MergeText is fairly efficient especially if you keep to expressions and values rather than code blocks.

### Merge Example
```
Hello World from <%= Version() %>.

The current time is: <%= time() %>.

If a table is open or a variable is marked as PRIVATE you can access
those values directly:

<%= myopenalias.field %>
<%= pcPrivateVar %>

The same goes for object references:


You can also embed UDF functions and object references (objects must be PRIVATE):

<%= MyUDFFunction %>
<%= poObject.DoSomething() %>

You can also embed full code blocks:

Here's a customer list:
<%
SELECT * FROM TT_CUST INTO CURSOR TQuery
pcOutput = ""
SCAN
   pcOutput = pcOutput + TQuery.Company + "<br>"
ENDSCAN

RETURN pcOutput
%>
```

Remember that you can change the merge parameters. This feature is by no means limited to HTML files. For example, the Web Connection Management Console uses this method to create new template driven programs based on choices made in the various Wizards. Check out the \templates directory and search for the <% %> tags for examples on how this works.