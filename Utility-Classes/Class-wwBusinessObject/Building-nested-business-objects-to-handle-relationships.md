Business objects of this type are powerful because they are fully self contained and can be aggregated easily without loosing their context. This works real well for modelling real life processes and objects. The benefit of aggregation in this manner is that a single object becomes a container that can be easily passed around with a single object reference whether its over VFP function or method calls, COM or by using XML to pass data back and forth over the Web.

For example, an invoice object tends to consist of a main invoice object, a customer object and a lineitems collection. Using the wwBusiness framework expressing this relationship is easy:
To handle this configuration the Load() event of the Invoice object is overloaded like this:

```foxpro
oInvoice
  oCustomer
  oLineItems
     oRows
        oRows[1]
```s

To handle this configuration the `Load()` event of the Invoice object is overloaded like this:

```foxpro
LPARAMETERS lnpk
LOCAL loLItem

IF !THIS.Open()
   RETURN .F.
ENDIF
 
*** - 1 brings up the last invoice
IF lnPk = -1 && Last order
   THIS.query("select MAX(pk) as PK from wws_invoice","TQuery")
   lnPK = TQuery.pk
ENDIF

*** Start by loading the invoice data
IF !DoDefault(lnPK)
   RETURN .F. 
ENDIF

*** Load LineItems
loLI = CREATE("cLineItems")
loLI.SetSqlObject(THIS.osql)
THIS.oLineItems = loLI.LoadLineItems(lnPK)</pre>

*** Load Customer
THIS.oCustomer = CREATE("cCustomer")
THIS.ocustomer.SetSQLObject(THIS.osql)

THIS.oCustomer.Load(THIS.oData.CustPK)
THIS.cShipCountry = THIS.oCustomer.oData.CountryId
```

That's it! BTW, note how the oSQL reference is passed to the child object by calling the SetSQLObject() method with the local sql object.

Ok, there's some logic in load lineitems that takes a little more code, but that's because I chose to implement the lineitem handling in the invoice rather than in the lower level class. Here's the code for that:

```foxpro
LPARAMETERS lnParentpk,lnlookuptype, lcName
LOCAL x

*** Override default behavior, since we're dealing with
*** a set of items instead of just one
lnlookuptype=IIF(EMPTY(lnlookuptype),1,lnlookuptype)
lcName=IIF(EMPTY(lcName),THIS.cSQLCursor,lcName)

DO CASE
   CASE THIS.ndatamode = 0
      SELECT * ;
         FROM (THIS.cDataPath + THIS.cFileName) ;
         WHERE InvPK = lnParentpk ;
         INTO CURSOR (lcName) NOFILTER
   CASE THIS.ndatamode = 2
      THIS.osql.cSQLCursor = lcName
      lnResult = THIS.osql.Execute("select * FROM " + THIS.cFileName + " WHERE InvPK=" + TRANSFORM(lnParentpk))
      IF lnResult # 1
         THIS.seterror(THIS.osql.cErrorMsg)
          RETURN .NULL.
      ENDIF
ENDCASE

loIL = CREATEOBJECT("cItemList")
 loIL.LoadFromCursor()
 USE IN (lcName)
RETURN loIL
```

The cItemList object's LoadFromCursor() method creates an object that contains a collection of object from each record in the cursor. The object contains a count and the array and each of these objects can be easily read into a full item object like this:

```foxpro
oLineItem = CREATE("cLineItem")
oLineItem.oData = oItemList.oRows.Item(1)
IF !oLineItem.Validate()
   RETURN .F.
ENDIF
oLineItem.Save()
```

Lest you think this process of dumping to objects and arrays is slow - it's not unless you're dealing with large amounts of data at time, which in theory should never be the case since these object level operations are geared towards object/record level operations. However, you can of course choose to implement differently. For example if it turns out that the list of children is too large or too slow to fit into an array you can always use a cursor to store the data instead and work with the cursor. It's up to you.

Now in the above scenario of the invoice, Save() is also overloaded to handle automatically updating the customer info and the lineitems when the invoice is saved:

```foxpro
LOCAL loLI, loItem, x

*** Start by saving the customer info
IF !ISNULL(THIS.oCustomer)
   THIS.oData.CustPK = THIS.oCustomer.oData.Pk
   IF !THIS.oCustomer.Save()
       THIS.SetError(THIS.ocustomer.cErrorMsg)
       RETURN .F.
   ENDIF
ENDIF

*** Make sure to recalc for save operation
THIS.InvoiceTotal()

*** Now save the lineitems too
IF ISNULL(THIS.oLineItems)
   RETURN .T.  && No lineitems
ENDIF


IF !THIS.oLineItems.SaveItems()
   RETURN .F.
ENDIF

*** Save the invoice record
llResult = DoDefault()

RETURN llResult
```

Again a little more work happens in SaveItems() - the items are actually deleted and then all added back in. The business logic for handling this best is left to you though - obviously that's not the most efficient way to do this, but in the case of this application it's plenty fast enough...


**Persisting to XML**  
These business and their hierarchies are self contained and fully disconnected while you're working on the data. Once the invoice object is loaded it's completely disconnected from the database. Nothing is open - everything is contained in the object. This makes it super easy to pass the object to another method/function or even application and even over the Web using XML.

To create XML of the entire business object:

```foxpro
oInv.Load(1)
oInv.ConvertData(6)
lcXML = oInv.cResultXML

*** Post the XML to the Web Server
oIP = CREATE("wwHTTP")
oIP.HTTPGet("http://www.west-wind.com/uploadinvoice.wws",lcXML)
```
You can take this XML and pass it across the Web to another application that picks it up. On the other end the application could receive it in Web Connection like this:

```foxpro
FUNCTION UploadInvoice

lcXML = Request.FormXML()

oInv = CREATE("cInvoice")
oInv.Load(0)   && Load empty invoice which loads all other blank objects

oXML = CREATE("wwXML")
oXML.nRecurseObjects=.T.
oXML.XMLToObject(lcXML,oInv)

* Response.Write( oInv.oData.InvNo )
* Response.Write( oInv.oCustomer.oData.Company )
* Response.Write( oInv.oLineItems.aRows[1].Descript)

IF  !oInv.Validate()
   Response.Write("Error: " + oInv.cErrorMsg)
   RETURN
ENDIF

*** Save the invoice
oInv.nUpdateMode = 2  && New
oInv.Save()

Response.Write("OK")

ENDFUNC
```

Voila you've taken an invoice entered on the client and moved it to the Web server for a fully distributed object application. In this case this works because the same business objects are running both on the client and server and you can simply import the XML and call the Save() method to write the new invoice.

The power of this kind of object relationship and self-contained structure makes it very easy to build distributed applications.