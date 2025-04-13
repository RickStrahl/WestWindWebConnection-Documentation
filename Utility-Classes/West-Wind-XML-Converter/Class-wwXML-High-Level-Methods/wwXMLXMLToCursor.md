This high level method takes an XML file created with CursorToXML and converts it back into a cursor. The method can create a cursor automatically, or use an input file you pass in (via a passed open alias name). In order to create the file a DTD like the one exported by [CursorToXML](vfps://Topic/wwXML%3A%3ACursorToXML) must be present in order to provide the Type, Size and Precision attributes required to create the cursor.

You can also generically import XML as long as the basic structure is followed:

```
<xdoc>
   <cursorname>
      <row>
          <data1/>
          <data2/>
      </row>
   </cursorname>
</xdoc>
```

The names of the elements are not relevant, but the hierarchy of document - root, cursor, row, field - must be intact.

In order to create the table on the fly the data fields need to have *name, type, size* and *precision* attributes, which can be provided either via DTD or with each field. See the XML document format below for an example.

Also check out the low level functions: [BuildCursorFromXML()](vfps://Topic/wwxml%3A%3ABuildCursorFromXML) which uses the field attributes to create the cursor and ParseXMLToCursor() which retrieves the XML into the actual cursor/table. If the table already exists ParseXMLToCursor can import the data generically as long as the relative structure of <cursor>, <row> and <data> exists.

**Importing exports from SQL Server and VFP's CursorToXML native function**  
wwXML can import data from SQL Server and VFP 7's CursorToXML XML formats, but it requires that the table structures preexist. In other words, wwXML can import but not use the schemas by these services (which differ significantly and are in constant flux). The following code demonstrates how to import a cursor in this fashion:

```foxpro
USE tt_cust
CURSORTOXML("tt_cust","lcXML",1,48,0,"")
USE

oXML = CREATEOBJECT("wwXML")
USE d:\temp\test.DBF EXCLUSIVE

* or (better perf)  SELECT * FROM TT_CUST WHERE .F. READWRITE INTO CURSOR TEST 
oXML.XMLToCursor(lcXML,"Test",1)
BROWSE
```