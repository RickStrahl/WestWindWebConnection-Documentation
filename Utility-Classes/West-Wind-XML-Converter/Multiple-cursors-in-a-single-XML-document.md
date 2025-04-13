It's possible to create XML documents that contain multiple cursors and or objects in a single XML document with wwXML.  

The following example demonstrates how to create two cursors in a single XML document:

```foxpro
CLOSE ALL
DO WCONNECT
#INCLUDE WCONNECT.h

LOCAL oXML as wwXML
oXML = CREATEOBJECT('wwXML')

* ** Generate customer XML and Schema
use TT_Cust
lcSchema = oXML.CreateDataStructureSchema("customers","customer",,"Customers")
lcXML = oXML.CreateCursorXML("customers","customer",,"#Customers")

* ** Append banner file XML and schema to each
use wwbanners
lcSchema = llcSchema + oXML.CreateDataStructureSchema("banners","banner",,"Banners")
lcXML = lcXML +  oXML.CreateCursorXML("banners","banner",,"#Banners")

* ** Now add a header and root document node 
lcXML = oXML.cXMLHeader + "<root>" + CRLF + lcSchema + lcXML + "</root>"

SHOWXML(lcXML)
CLOSE DATABASES
```

Note that this code generates optional schemas for both cursors which are embedded into the document. The key to this code using the low level methods CreateCursorToXML and for the schemas CreateDataStructureSchema to generate each of the XML fragments seperately and then stringing them together into a final string at the end ofthe code.

This generates the following XML document:

```xml
<?xml version="1.0"?>
<root>
<Schema name="Customers" xmlns="urn:schemas-microsoft-com:xml-data" xmlns:dt="urn:schemas-microsoft-com:datatypes">
   <ElementType name="customers" content="eltOnly" model="closed" order="many">
		<element type="customer" />
	</ElementType>
	<ElementType name="customer" content="eltOnly" model="closed" order="many">
		<element type="custno"/>
		<element type="company"/>
		<element type="careof"/>
		<element type="address"/>
		<element type="shipaddr"/>
		<element type="email"/>
		<element type="phone"/>
		<element type="billrate"/>
		<element type="btype"/>
		<element type="entered"/>
		<element type="pk"/>
	</ElementType>
	<ElementType name="custno" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
		<AttributeType name="size"/>
		<attribute type="size" default="8"/>
	</ElementType>
	<ElementType name="company" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
		<AttributeType name="size"/>
		<attribute type="size" default="30"/>
	</ElementType>
	<ElementType name="careof" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
		<AttributeType name="size"/>
		<attribute type="size" default="30"/>
	</ElementType>
	<ElementType name="address" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
		<AttributeType name="size"/>
		<attribute type="size" default="2147483647"/>
	</ElementType>
	<ElementType name="shipaddr" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
		<AttributeType name="size"/>
		<attribute type="size" default="2147483647"/>
	</ElementType>
	<ElementType name="email" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
		<AttributeType name="size"/>
		<attribute type="size" default="2147483647"/>
	</ElementType>
	<ElementType name="phone" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
		<AttributeType name="size"/>
		<attribute type="size" default="2147483647"/>
	</ElementType>
	<ElementType name="billrate" content="textOnly" model="closed" dt:type="float">
		<AttributeType name="type"/>
		<attribute type="type" default="float"/>
		<AttributeType name="size"/>
		<attribute type="size" default="7"/>
		<AttributeType name="precision"/>
		<attribute type="precision" default="2"/>
	</ElementType>
	<ElementType name="btype" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
		<AttributeType name="size"/>
		<attribute type="size" default="15"/>
	</ElementType>
	<ElementType name="entered" content="textOnly" model="closed" dt:type="datetime">
		<AttributeType name="type"/>
		<attribute type="type" default="datetime"/>
		<AttributeType name="size"/>
		<attribute type="size" default="8"/>
	</ElementType>
	<ElementType name="pk" content="textOnly" model="closed" dt:type="i4">
		<AttributeType name="type"/>
		<attribute type="type" default="i4"/>
		<AttributeType name="size"/>
		<attribute type="size" default="4"/>
	</ElementType>
</Schema>
<Schema name="Banners" xmlns="urn:schemas-microsoft-com:xml-data" xmlns:dt="urn:schemas-microsoft-com:datatypes">
   <ElementType name="banners" content="eltOnly" model="closed" order="many">
		<element type="banner" />
	</ElementType>
	<ElementType name="banner" content="eltOnly" model="closed" order="many">
		<element type="bannerid"/>
		<element type="image"/>
		<element type="link"/>
		<element type="redirect"/>
		<element type="imgtags"/>
		<element type="hreftags"/>
		<element type="order"/>
		<element type="type"/>
		<element type="hits"/>
		<element type="maxhits"/>
		<element type="clicks"/>
		<element type="timestamp"/>
		<element type="number"/>
		<element type="currency"/>
		<element type="floaty"/>
		<element type="doublee"/>
	</ElementType>
	<ElementType name="bannerid" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
		<AttributeType name="size"/>
		<attribute type="size" default="9"/>
	</ElementType>
	<ElementType name="image" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
		<AttributeType name="size"/>
		<attribute type="size" default="75"/>
	</ElementType>
	<ElementType name="link" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
		<AttributeType name="size"/>
		<attribute type="size" default="128"/>
	</ElementType>
	<ElementType name="redirect" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
		<AttributeType name="size"/>
		<attribute type="size" default="128"/>
	</ElementType>
	<ElementType name="imgtags" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
		<AttributeType name="size"/>
		<attribute type="size" default="40"/>
	</ElementType>
	<ElementType name="hreftags" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
		<AttributeType name="size"/>
		<attribute type="size" default="40"/>
	</ElementType>
	<ElementType name="order" content="textOnly" model="closed" dt:type="float">
		<AttributeType name="type"/>
		<attribute type="type" default="float"/>
		<AttributeType name="size"/>
		<attribute type="size" default="2"/>
	</ElementType>
	<ElementType name="type" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
		<AttributeType name="size"/>
		<attribute type="size" default="15"/>
	</ElementType>
	<ElementType name="hits" content="textOnly" model="closed" dt:type="i4">
		<AttributeType name="type"/>
		<attribute type="type" default="i4"/>
		<AttributeType name="size"/>
		<attribute type="size" default="4"/>
	</ElementType>
	<ElementType name="maxhits" content="textOnly" model="closed" dt:type="i4">
		<AttributeType name="type"/>
		<attribute type="type" default="i4"/>
		<AttributeType name="size"/>
		<attribute type="size" default="4"/>
	</ElementType>
	<ElementType name="clicks" content="textOnly" model="closed" dt:type="i4">
		<AttributeType name="type"/>
		<attribute type="type" default="i4"/>
		<AttributeType name="size"/>
		<attribute type="size" default="4"/>
	</ElementType>
	<ElementType name="timestamp" content="textOnly" model="closed" dt:type="datetime">
		<AttributeType name="type"/>
		<attribute type="type" default="datetime"/>
		<AttributeType name="size"/>
		<attribute type="size" default="8"/>
	</ElementType>
	<ElementType name="number" content="textOnly" model="closed" dt:type="float">
		<AttributeType name="type"/>
		<attribute type="type" default="float"/>
		<AttributeType name="size"/>
		<attribute type="size" default="10"/>
		<AttributeType name="precision"/>
		<attribute type="precision" default="4"/>
	</ElementType>
	<ElementType name="currency" content="textOnly" model="closed" dt:type="number">
		<AttributeType name="type"/>
		<attribute type="type" default="number"/>
		<AttributeType name="size"/>
		<attribute type="size" default="20"/>
		<AttributeType name="precision"/>
		<attribute type="precision" default="4"/>
	</ElementType>
	<ElementType name="floaty" content="textOnly" model="closed" dt:type="float">
		<AttributeType name="type"/>
		<attribute type="type" default="float"/>
		<AttributeType name="size"/>
		<attribute type="size" default="20"/>
		<AttributeType name="precision"/>
		<attribute type="precision" default="6"/>
	</ElementType>
	<ElementType name="doublee" content="textOnly" model="closed" dt:type="number">
		<AttributeType name="type"/>
		<attribute type="type" default="number"/>
		<AttributeType name="size"/>
		<attribute type="size" default="8"/>
	</ElementType>
</Schema>
<customers xmlns="x-schema:#Customers">
	<customer>
		<custno>4</custno>
		<company>West Wind Technologies</company>
		<careof>Rick Strahl</careof>
		<address>9411 Broadway
New York, NY 11111</address>
		<shipaddr/>
		<email/>
		<phone>(808) 579-8342</phone>
		<billrate>50.00</billrate>
		<btype>Labor_PRG</btype>
		<entered>2001-09-23T16:10:46</entered>
		<pk>4</pk>
	</customer>
	<customer>
		<custno>6</custno>
		<company>Golf Reproduction</company>
		<careof>Jack Olson</careof>
		<address>42 Tabletop Ct.
Copperopolis, CA   95228</address>
		<shipaddr/>
		<email/>
		<phone>Office: (209) 786-5xxx
Jack: (510) 825-9xxx
New Remote: (408) 224-3xxx</phone>
		<billrate>45.00</billrate>
		<btype>Labor_PRG</btype>
		<entered>2001-09-21T19:44:06</entered>
		<pk>6</pk>
	</customer>
</customers>
<banners xmlns="x-schema:#Banners">
	<banner>
		<bannerid>S7J0O157V</bannerid>
		<image>http://www.west-wind.com/banners/bugcentral.gif</image>
		<link>wc.wc?wwDemo~Banners~&amp;Action=Click&amp;ID=S7J0O157V</link>
		<redirect>http://www.bugcentral.com/</redirect>
		<imgtags/>
		<hreftags/>
		<order>0</order>
		<type/>
		<hits>15</hits>
		<maxhits>0</maxhits>
		<clicks>6</clicks>
		<timestamp>2000-07-03T13:58:23</timestamp>
		<number/>
		<currency>413122.2322</currency>
		<floaty>31321.456780</floaty>
		<doublee>222</doublee>
	</banner>
	<banner>
		<bannerid>SB50WT343</bannerid>
		<image>/wconnect/banners/wwhelpbanner.gif</image>
		<link>wc.wc?wwDemo~Banners~&amp;Action=Click&amp;ID=SB50WT343</link>
		<redirect>http://localhost/wwhelp</redirect>
		<imgtags>vspace=20</imgtags>
		<hreftags/>
		<order>0</order>
		<type/>
		<hits>15</hits>
		<maxhits>0</maxhits>
		<clicks>9</clicks>
		<timestamp>2000-07-03T13:58:23</timestamp>
		<number/>
		<currency>0.0000</currency>
		<floaty>123.232221</floaty>
		<doublee>21312</doublee>
	</banner>
</banners>
</root>
```

Since this XML document was created with a schema we can now turn this back into XML with the following code:

```foxpro
lcXML = FILETOSTR("saved.xml") && XML from above

oDom = CREATEOBJECT("MSXML2.DOMDocument")
oDOM.Async = .F.
oDOM.LoadXML(lcXML)
IF !EMPTY(oDOm.parseError.reason)
  ? oDOm.parseError.reason
  RETURN
ENDIF

loCustomers = oDOM.selectSingleNode("//root/customers")
IF !ISNULL(loCustomers)
   oXML.BuildCursorFromXml(loCustomers,"TCustomers")
   oXML.ParseXMLToCursor(loCustomers)
   BROWSE
ENDIF

loBanners = oDOM.selectSingleNode("//root/banners")
IF !ISNULL(loCustomers)
   oXML.BuildCursorFromXml(loBanners,"TBanners")
   oXML.ParseXMLToCursor(loBanners)
   BROWSE
ENDIF
```

The XMLDOM parser is used to load the XML document and then navigate to each of the table level nodes. The calls to BuildCursorFromXML read the schema and create the cursor while ParseXMLToCursor reads the XML data into the cursor.

Note that this works with Schemas only - you cannot have  multiple DTDs in a single document - this is one of the advantages of Schemas as they can apply to sections of the document as opposed to the whole document at once.

The same scheme can be applied to XML without schemas as well. But you will have to have a cursor to import the data into ready on your own. If you do you can just use ParseXMLToCursor() to import the data only.

Embedded schemas as used here require MSXML 2.5 or later.