If .T. Request.Form retrieves variables from an XML document contained in the form buffer rather than regular post variables.

This powerful feature allows you to transparently accept input from XML clients rather than standard HTML based Web pages. You can simply check for XML inputs as follows:

<pre>lcXML = Request.FormXML()
IF lcXML = "<?"
   Request.lXMLFormVars = .T.
   llXML = .T.
ELSE
   llXML = .F.
ENDIF

* ** Now continue reading form vars
lcName = Request.Form("Name")
</pre>

This simple bit of logic allows you to simultaneously serve HTML and XML clients with an identical code base (although your output will probably have to XML formatted as well.