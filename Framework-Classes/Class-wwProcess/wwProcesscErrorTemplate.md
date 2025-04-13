This method allows overriding the default error display page using a template page called through ExpandTemplate.  If set specifies a full physical disk path to a page that is used for ErrorMsg and StandardPage output.

The template should include the following elements at a minimum:

<pre><html>
	<head>
	   <meta http-equiv="Content-Language" content="en-us">
	   <title><%= pcHeader %></title>
	   <link rel="stylesheet" type="text/css" href="westwind.css">  <!-- Optional -->
 	   <%= IIF(pnRefresh>0,[<META HTTP-EQUIV="Refresh" CONTENT="]+TRANS(pnRefresh)+ [; URL=]+pcRefreshUrl + [">],[wwSoap::cHttpProxy]) %>
	</head>
<body topmargin=0 leftmargin=0>
<h1><%= pcHeader %></h1>
<hr>
<%= pcBody %>
</body></pre>

The variables exposed are the same as the parameters to ErrorMsg except with a 'p' (for PRIVATE) instead of 'l' (LOCAL) first letter.

pcHeader - header text
pcBody - body text
pcRefreshUrl - Url to redirect to if provided
pnRefresh - Time in seconds to redirect if non zero