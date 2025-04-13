Links in an external or internal schema. Use #SchemaName for internal schemas and a fully qualified or relative URL for external schemas.

The schemas must be compatible with the CreateDataStructureSchema's structure. The schema is attached to the table level node of the document not to the document root.

**At the table level:**  
<pre><docroot>
<customers xmlns="x-schema:/wconnect/dataschema.xml'>
	<customer>
		<custno>       2</custno>
		<company>BGP Productions</company>
 	</customer>
</customers>
</docroot></pre>

**At the object level:**  
<pre><?xml version="1.0"?>
<docroot>
	<class xmlns="x-schema:#/wconnect/objectschema.xml">
		<colors></colors>
		<cost>0</cost>
	</class>
</xdoc></pre>