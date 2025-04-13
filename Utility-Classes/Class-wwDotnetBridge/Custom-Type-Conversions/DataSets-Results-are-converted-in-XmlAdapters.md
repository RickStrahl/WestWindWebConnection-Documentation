Web Services that return DataSet results as result values are automatically marshalled into Visual FoxPro XmlAdapters. For example:

```foxpro
DO WebStoreServiceProxy
LOCAL loService as WebStoreServiceProxy
loService = CREATEOBJECT("WebStoreServiceProxy")

LOCAL loAdapter as XMLAdapter
loAdapter = loService.DownloadInventoryItemsDataSet("")

IF ISNULL(loAdapter)
? loService.cErrorMsg	
    RETURN
ENDIF

* ** Use simple helpers in wwDotNetBridge
loService.oBridge.XmlAdapterToCursors(loAdapter)  && Convert all tables
loService.oBridge.XmlAdapterGetCursor(loAdapter,"itemlist")  && Convert specific table

* ** Or use standard XmlAdapter code

* ** Access an individual table
loTable = loAdapter.Tables[1]
USE IN SELECT(loTable.Alias)
? loTable.Alias
loTable.ToCursor(.F.,loTable.Alias)

* ** Loop through all tables and create cursors from them
FOR EACH loTable IN loADapter.Tables
   USE IN SELECT(loTable.Alias) 
   loTable.ToCursor(.F.,lotable.Alias)
ENDFOR
```

Note that the result is returned as a standard Visual FoxPro XMLAdapter object. You should check for a NULL result from the function for errors to ensure you trap any errors that might have occurred during the service call. You can then loop through the Tables collection of the XmlAdapter and use the ToCursor() method to dump tables to disk.

### XmlAdapter Helper Methods on wwDotNetBridge
An XML Adapter is a better result value than a .NET DataSet (which can't be easily accessed by vfp due its type format), but even so XmlAdapters are a bit unwieldy and they suffer from a horribly unintuitive API implementation. 

To faciliate converting DataSets to cursors a couple of helper functions have been provided in wwDotNetBridge which is exposed on the service proxy class as loService.oBridge. You can use the following two methods:

```foxpro
? loService.oBridge.XmlAdapterToCursors(loAdapter)
```

which turns all Tables in the XmlAdapters into cursors much in the same way as the code shown in the last example.

```foxpro
? loService.oBridge.XmlAdapterGetCursor(loAdapter, "ItemList")
? loService.oBridge.XmlAdapterGetCursor(loAdapter, 1)
```

retrieves an individual cursor from a data set by name or index. This saved a couple of steps and trying to remember the XmlAdapter syntax and checking for closing of tables etc.

Both methods return .T. if cursors were created or .F. on failure.

### Web Service Method Implementation for DataSet Result Methods
If you're curious methods that return DataSets include the logic to convert the Dataset into an XmlAdapter automatically. If you look at the generated source for such a method you might see something like this:

```foxpro
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ****
*  DownloadInventoryItemsDataSet
* *** *** *** *** *** *** *** *** *** *** *** *** ***
FUNCTION DownloadInventoryItemsDataSet(Category as String) as DataSet
LOCAL loException as Exception, lvResult as Object

THIS.lError = .F.
this.cErrorMsg = ""

lvResult = .F.
TRY
	lvResult = this.oService.DownloadInventoryItemsDataSet(Category)

**	* ** Turn the result into a XmlAdapter - use loAdapter.Tables[0] to access cursors
	lvResult = THIS.oBridge.DatasetToXmlAdapter(lvResult)
**  
CATCH to loException
	llError = .T.
	this.cErrorMsg = loException.Message
ENDTRY

RETURN lvResult
ENDFUNC
*   DownloadInventoryItemsDataSet
```
 
The proxy actually receives the native .NET DataSet and then internally calls on a wwDotNetBridge helper method to turn the DataSet into an XmlAdapter.