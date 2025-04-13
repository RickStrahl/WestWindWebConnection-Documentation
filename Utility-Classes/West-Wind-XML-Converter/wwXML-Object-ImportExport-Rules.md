### Object Exports
XML exports can almost universally be created from all VFP data structures. There is only 1 exception:

* Array properties are exported one dimensionally. If multiple dimensions are available the array is flattened into 1D.

### Object Imports
Importing occurs by looking at the structure of an object and based on that structure pulling the data from the XML structure via the Microsoft XMLDom parser.

In order to import from XML your XML must either contain a wwXML compatible DTD, or provide an object instance for the object to be filled. The instance should have all values and subobjects filled with properly typed values.


### Arrays and Collection recursive imports
Arrays and collection imports require special handling in wwXML. The export format is fairly standard but in order to import a few custom tags are used that describe some limited type information to make it possible to import the arrays. 

In order for arrays and collections to export and import  the lRecurseObjects property must be set to .T. 

Collection imports make the following assumptions:
* If the collection consists of simple values the items are always imported
* The first item in the array member must exist and be set to the correct type when importing objects
* If you want to import a collection of objects of the same type, you have to add a single object to the collection. The object should have all of its members and child objects set to at least blank values that are properly typed.
* If you don't provide a 'template' object the collection import skips objects

Array imports make the following assumptions:
* Only 1 dimensional arrays are supported. Multidimensional arrays are flattened into 1 D Arrays. 
* The first item in the array determines the type of all elements.
* The first item in the array member must exist and be set to the correct type when importing objects

These requirements arise out of the parser having to know the type that the item to be parsed. Without a predefined type this information is not available since data stored in XML is always text.

Here's an example of how to export and then import a dynamic object with collections:

```foxpro
*** Create some sample objects
loAddress = CREATEOBJECT("Address")
loAddress.cName = "Rick Strahl"
loAddress.cAddress = "32 Kaiea"

loAddress2 = CREATEOBJECT("Address")
loAddress.cName = "Markus Egger"
loAddress.cAddress = "400 Hexham"


*** Create the host class and add a property and Collection
loContainer = CREATEOBJECT("Empty")
ADDPROPERTY(loContainer,"objecttype","CollectionTest")

*** Add a collection and add the addresses as objects
ADDPROPERTY(loContainer,"Addresses",CREATEOBJECT("Collection"))
loContainer.Addresses.Add(loAddress,"address1")
loContainer.Addresses.Add(loAddress2,"address2")

oXML = CREATEOBJECT("wwXML")
oXML.lRecurseobjects = .T.
lcXml = oXML.ObjectToXML(loContainer)
```

This will generate the following XML:

```xml
<?xml version="1.0"?>
<xdoc>
	<class type="O" class="Empty">
		<addresses type="O" class="Collection">
			<count>2</count>
			<keysort>0</keysort>
			<items>
				<item key="address1" type="O" class="Address">
					<caddress>400 Hexham</caddress>
					<cname>Markus Egger</cname>
				</item>
				<item key="address2" type="O" class="Address">
					<caddress></caddress>
					<cname></cname>
				</item>
			</items>
		</addresses>
		<objecttype>CollectionTest</objecttype>
	</class>
</xdoc>
```

Ok. Now let's turn this back into an object. As mentioned the collection includes a set of objects so in order for the import to work we have to pre-seed the collection's first item with an object. Here's the code:

```foxpro
*** Create the same object as above
loContainer = CREATEOBJECT("Empty")
ADDPROPERTY(loContainer,"objecttype","")
ADDPROPERTY(loContainer,"Addresses",CREATEOBJECT("Collection"))

*** Now must add the blank Address object into collection
loContainer.Addresses.Add(loAddress,"address1")

*** Now import into this object from our XML string
oXML = CREATEOBJECT("wwXML")
oXML.lRecurseobjects = .T.
oXML.XmlToObject(lcXML,loContainer)


*** Print out the imported data (2 items)
FOR x = 1 TO loContainer.Addresses.Count
  ? loContainer.Addresses[x].cName
  ? loContainer.Addresses[x].cAddress
  ?
ENDFOR

*** Now we can convert
oXML.XmlToObject(lcXML,loContainer)

*** Print out the imported data
FOR x = 1 TO loContainer.Addresses.Count
  ? loContainer.Addresses[x].cName
  ? loContainer.Addresses[x].cAddress
  ?
ENDFOR
```

The same sort of thing needs to be done with Array exports. For arrays populate the first item with the object in question.