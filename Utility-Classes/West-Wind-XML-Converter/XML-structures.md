The various methods that handle data translation between FoxPro data tables and XML are based on a specific format.  Remember that Schema and DTD use with the nCreateDataStructure property are optional. Data can be created without schemas or DTDs if the receiving application has access to the underlying table structure. This can reduce size and improve performance slightly. However, generally we suggest that you do send a schema so the client application can create data structures (tables and/or objects) on its own if necessary.

The following basic XML structures are available and are listed with example code to create them:

### Cursor Export from CursorToXML() with Schema:
```foxpro
USE TT_CUST
*** Create an empty cursor
SELECT * FROM TT_CUST WHERE RECNO() < 3 INTO Cursor TQuery

oXML = CREATE("wwXML")
oXML.cDocRootName = "wwxmldemo"
oXML.nCreateDataStructure = 1  && Schema (optional)
lcXML = oXML.CursorToXML("customers","customer")   && Name the table and row elements
```
```html
<?xml version="1.0"?>
<wwxmldemo>
<Schema name="Schema" xmlns="urn:schemas-microsoft-com:xml-data" xmlns:dt="urn:schemas-microsoft-com:datatypes">
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
<customers xmlns="x-schema:#Schema">
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
</wwxmldemo>
```

### Single level object XML with Schema created with ObjectToXML():
```foxpro
DO LOAD_wwStore

oItem = CREATEOBJECT("cITem")
oItem.GetItembySku("WCONNECT")

oXML = CREATEOBJECT("wwXML")
oXML.cDocRootName = "wwstore"  && Optional
oXML.nCreateDataStructure = 1
lcXML = oXML.ObjectToXML(oItem.oData,"item")  && Name object level element</pre>
<hr>
<pre><?xml version="1.0"?>
<wwstore>
<Schema name="Schema" xmlns="urn:schemas-microsoft-com:xml-data" xmlns:dt="urn:schemas-microsoft-com:datatypes">
	<ElementType name="item" content="eltOnly" model="closed" order="many">
		<element type="categories"/>
		<element type="colors"/>
		<element type="cost"/>
		<element type="descript"/>
		<element type="emailto"/>
		<element type="expected"/>
		<element type="image"/>
		<element type="keywords"/>
		<element type="ldescript"/>
		<element type="mfr"/>
		<element type="noweb"/>
		<element type="onorder"/>
		<element type="physical"/>
		<element type="pk"/>
		<element type="price"/>
		<element type="savemail"/>
		<element type="sizes"/>
		<element type="sku"/>
		<element type="sortorder"/>
		<element type="special"/>
		<element type="stock"/>
		<element type="storepk"/>
		<element type="type"/>
		<element type="url"/>
		<element type="weight"/>
		<element type="xml"/>
	</ElementType>
	<ElementType name="categories" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
	</ElementType>
	<ElementType name="colors" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
	</ElementType>
	<ElementType name="cost" content="textOnly" model="closed" dt:type="float">
		<AttributeType name="type"/>
		<attribute type="type" default="float"/>
	</ElementType>
	<ElementType name="descript" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
	</ElementType>
	<ElementType name="emailto" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
	</ElementType>
	<ElementType name="expected" content="textOnly" model="closed" dt:type="date">
		<AttributeType name="type"/>
		<attribute type="type" default="date"/>
	</ElementType>
	<ElementType name="image" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
	</ElementType>
	<ElementType name="keywords" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
	</ElementType>
	<ElementType name="ldescript" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
	</ElementType>
	<ElementType name="mfr" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
	</ElementType>
	<ElementType name="noweb" content="textOnly" model="closed" dt:type="boolean">
		<AttributeType name="type"/>
		<attribute type="type" default="boolean"/>
	</ElementType>
	<ElementType name="onorder" content="textOnly" model="closed" dt:type="float">
		<AttributeType name="type"/>
		<attribute type="type" default="float"/>
	</ElementType>
	<ElementType name="physical" content="textOnly" model="closed" dt:type="boolean">
		<AttributeType name="type"/>
		<attribute type="type" default="boolean"/>
	</ElementType>
	<ElementType name="pk" content="textOnly" model="closed" dt:type="float">
		<AttributeType name="type"/>
		<attribute type="type" default="float"/>
	</ElementType>
	<ElementType name="price" content="textOnly" model="closed" dt:type="float">
		<AttributeType name="type"/>
		<attribute type="type" default="float"/>
	</ElementType>
	<ElementType name="savemail" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
	</ElementType>
	<ElementType name="sizes" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
	</ElementType>
	<ElementType name="sku" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
	</ElementType>
	<ElementType name="sortorder" content="textOnly" model="closed" dt:type="float">
		<AttributeType name="type"/>
		<attribute type="type" default="float"/>
	</ElementType>
	<ElementType name="special" content="textOnly" model="closed" dt:type="float">
		<AttributeType name="type"/>
		<attribute type="type" default="float"/>
	</ElementType>
	<ElementType name="stock" content="textOnly" model="closed" dt:type="float">
		<AttributeType name="type"/>
		<attribute type="type" default="float"/>
	</ElementType>
	<ElementType name="storepk" content="textOnly" model="closed" dt:type="float">
		<AttributeType name="type"/>
		<attribute type="type" default="float"/>
	</ElementType>
	<ElementType name="type" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
	</ElementType>
	<ElementType name="url" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
	</ElementType>
	<ElementType name="weight" content="textOnly" model="closed" dt:type="float">
		<AttributeType name="type"/>
		<attribute type="type" default="float"/>
	</ElementType>
	<ElementType name="xml" content="textOnly" model="closed" dt:type="string">
		<AttributeType name="type"/>
		<attribute type="type" default="string"/>
	</ElementType>
</Schema>
	<item xmlns="x-schema:#Schema">
		<categories>West Wind Products
Web Tools
Web Connection</categories>
		<colors></colors>
		<cost>0</cost>
		<descript>West Wind Web Connection 4.0</descript>
		<emailto></emailto>
		<expected></expected>
		<image>wconnect.gif</image>
		<keywords></keywords>
		<ldescript>Build Web applications...</ldescript>
		<mfr>West Wind Technologies</mfr>
		<noweb>0</noweb>
		<onorder>0</onorder>
		<physical>0</physical>
		<pk>3</pk>
		<price>399</price>
		<savemail></savemail>
		<sizes></sizes>
		<sku>WCONNECT</sku>
		<sortorder>99</sortorder>
		<special>0</special>
		<stock>0</stock>
		<storepk>0</storepk>
		<type></type>
		<url>http://www.west-wind.com/webconnection/</url>
		<weight>0</weight>
		<xml>&lt;properties&gt;&lt;UseOutlook type="boolean"&gt;0&lt;/UseOutlook&gt;&lt;/properties&gt;</xml>
	</item>
</wwstore>
```

### Hierarchical object (no data structure support via DTD or Schemas)
```foxpro
oInv = CREATEOBJECT("cInvoice")
oInv.Load( 852049 )

? oInv.oData.InvNo
? oInv.oData.InvDate

? oInv.oCustomer.oData.Company
? oInv.oCustomer.oData.Address

? oInv.oLineItems.aRows[1].Sku
? oInv.oLineItems.aRows[1].Descript

oXML = CREATEOBJECT("wwXML")
oXML.cdocrootname = "wwstore"
oXML.lrecurseobjects = .T.
lcXML = oXML.objecttoxml(oInv,"invoice")
ShowXML(lcXML)
```

Generating the following XML:

```foxpro
<?xml version="1.0"?>
<wwstore>
	<invoice>
		<cshipcountry>US</cshipcountry>
		<cshippingbehaviorclass>cShippingBehavior</cshippingbehaviorclass>
		<cshipstate></cshipstate>
		<ctaxbehaviorclass>cTaxBehavior</ctaxbehaviorclass>
		<lnoshippingtotal>0</lnoshippingtotal>
		<lnotaxtotal>1</lnotaxtotal>
		<nlineitemsmode>0</nlineitemsmode>
		<nsubtotal>0</nsubtotal>
		<ocustomer>
			<odata>
				<pk>809420</pk>
				<address>32 Kaiea Place</address>
				<city>Paia</city>
				<company>West Wind Technologies</company>
				<country>United States</country>
				<countryid>US</countryid>
				<downloaded>0</downloaded>
 				...
			</odata>
		</ocustomer>
		<odata>
			<cc>4123 1233 1234 1234</cc>
			<ccexp>01/2001</ccexp>
			<cctype>VI</cctype>
			<custpk>809420</custpk>
			<downloaded>0</downloaded>
			<handling>3</handling>
			<invdate>2000-07-25</invdate>
			<invno>05R115K5G24</invno>
			<invtotal>623.88</invtotal>
			<pk>852049</pk>
			<ponumber></ponumber>
			<shipby></shipby>
			<shipdisks>0</shipdisks>
			<shipping></shipping>
			<storepk>0</storepk>
			<tax>23.88</tax>
			<taxrate>0</taxrate>
			<weight>0</weight>
		</odata>
		<olineitems>
			<arows>
				<ncount>2</ncount>
				<arows_item>
					<custpk>809420</custpk>
					<descript>West Wind Web Connection 2.x Enterprise to 3.x Upg</descript>
					<discount>0</discount>
					<invpk>852049</invpk>
					<itempk>0</itempk>
					<itemtotal>398</itemtotal>
					<pk>351337</pk>
					<price>199</price>
					<qty>2</qty>
					<sku>WCENTUP</sku>
					<storepk>0</storepk>
					<weight>0</weight>
				</arows_item>
				<arows_item>
					<custpk>809420</custpk>
					<descript>West Wind HTML Help Builder</descript>
  					...
				</arows_item>
			</arows>
		</olineitems>
	</invoice>
</wwstore>
```

### XML Export Notes:
The document hierarchy is important if you plan on importing data via this model. The names of the tags are not relevant, but the generic imports rely on an isolated document root (xdoc) followed by the table or object name. In the case of tables each row has a header element tag which is followed by the data elements. Objects do the same but don't have the row information. Note, the names are irrevelant - it's the relationship between these (level 1,2,3) that allow these objects to be imported correctly.

The low level methods can accept an IE document node as input to allow you to parse an XML tree with the right structure from anywhere within an XML document. I presume any kind of XML document would have to have rows, row, fields relationship for tables and object/fields relationship for objects and you can use the parser to locate to these object nodes and then have ParseXMLToCursor or ParseXMLToObject import the XML.