This high level method creates a full XML document that exports a VFP cursor to an XML document. If you export the file with the [nCreateDataStructure](vfps://Topic/wwxml%3A%3AnCreateDataStructure) flag set to 1  the file will contain a Schema or 2 to contain a DTD that describes the data structure of the table.

XML created with this method can be converted back into a cursor by running [XMLToCursor()](vfps://Topic/wwXML%3A%3AXMLToCursor).

Note that the structure of the document is:

```xml
<docroot>
    <schema>  (optional)
 	</schema>
	<table>
		<row>
			<field></field>
		</row>
		<row>	
			<field></field>
		</row>	
 	</table>
</docroot>
``` 	

You can create an embedded schema by setting the [nCreateDataStructure](vfps://Topic/wwxml%3A%3AnCreateDataStructure) property to 1. To create a DTD use a value of 2. Schemas are embedded and may not work in all but the latest XML parsers that support them. You can reference an external schema using the [cSchemaUrl](vfps://Topic/wwxml%3A%3AcSchemaUrl) property. The schema should follow the rules of [CreateDataStructureSchema](vfps://Topic/wwXML%3A%3ACreateDataStructureSchema).