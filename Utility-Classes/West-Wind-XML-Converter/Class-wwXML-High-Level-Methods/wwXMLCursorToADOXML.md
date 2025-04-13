This method exports XML in the ADO compatible XML format. This format is very specific to ADO and although a valid XML structure is not a well designed document and unlikely to be used anywhere else. The output is the equivalent of doing **rs.save('filename',1)** from ADO.

This method is between 10-20% faster than the VFPCOM utilities CursorToRS export. To directly load the XML into an ADO recordset use the example listed below.

For full XML format see the [XML Structures](vfps://Topic/XML%20structures) topic.