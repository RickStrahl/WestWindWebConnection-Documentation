This class, subclasses from the `wwBusinessObject` class, is used to represent a list of objects as are commonly used in child objects in one to many relations. A typical example might be the lineitems of an invoice. This class uses an internal public `oRows` Collection member to contain the actual data items of the child data which can be loaded with the `LoadFromCursor()` method which loads the array members from a cursor. Since this object is subclasses from `wwBusinessObject` you can use it to manage both the list as well as individual items in that list.

You can use this mechanism as the offline storage for things like lineitems on an invoice for example. To actually manipulate each item you can do something like this to use the 'full' business object to use the data:

```foxpro
*** cLineItems here is subclassed from wwBusinessChildCollectionList
*** and is bound to a line items table
oInvoice.oLineItems = oInvoice.CreateChildObject("cLineItems")
oInvoice.oLineItems.LoadFromCursor()

*** Retrieve the first item of the collection and store it to the local oData member
oInvoice.oLineItems.oData  = oInvoice.oLineItems.Item(1)

*** Write it out
oInvoice.oLineItems.Save()
```

This would be using the default `.Save()` method which saves an individual lineitem.

Alternately you can use the `LoadFromCursor()` and `SaveToCursor()` methods to simply load and save the list as a whole:

```foxpro
oInvoice.oLineItems = oInvoice.CreateChildObject("cLineItems")
oInvoice.oLineItems.LoadFromCursor()

*** Retrieve the first item of the Collection and store it to the local oData member
oInvoice.oLineItems.oRows[1].Description = "New Description 1"
oInvoice.oLineItems.oRows[2].Description = "New Description 2"

*** Now go write back all the lineitems
oInvoice.oLineItems.SaveToCursor()
```

The code for both of these type of operations probably should be wrapped into the cLineitems class internally for better design and then have methods such as LoadLineItems() and SaveLineItems() which encapsulate this behavior.

**Parent Class: wwBusinessObject**