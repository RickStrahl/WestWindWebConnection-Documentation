The wwXMLState class allows you to add and retrieve properties easily from an XML string with a couple of simple lines of code.

**Example:**

```foxpro
SET PROCEDURE TO wwXMLState

USE SomeTable

o = CREATEOBJECT("wwXMLState")

* **Set a value
o.LoadXML(SomeTable.XMLPropertiesMemo)
o.SetProperty("testproperty","new value","string","Testing")
REPLACE SomeTable.XMLPropertiesMemo with o.cXML

* **Retrieve a value
o.cXML = SomeTable.XMLPropertiesMemo
lcValue = o.GetProperty("testproperty")
```

The XML format for the property storage looks like this:

```xml
<properties>
   <Test type="string" description="Test Value">Test Value</Test>
   <Number type="float">123.33</Number>
   <Bool type="boolean">1</Bool><Bool2 type="boolean">0</Bool2>
   <date type="datetime">12/28/2000</date>
   <datetime type="datetime">12/28/2000 1:07:25 AM</datetime>
</properties>
```

---
**Based on:** Relation  
**Stored in:** wwXMLState
---