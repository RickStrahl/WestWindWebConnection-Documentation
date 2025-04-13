Adds a cursor to the list of open cursors that should be automatically closed when the page Dispose() fires.

When using cursors it's common to run a query early in the page cycle and then databind the cursor say to a DataGrid. Since databinding occurs during the Render() phase there's really no clean way to close the cursor in a linear fashion. The only option you have is to explicitly implement a Dispose() method and close the cursor in this method.

RegisterCursor() automates this process by allowing you to procedurally add a cursor as soon as it's been loaded. Internally the page keeps track of the specified cursors and then closes them all in the .Dispose() method of the page ensuring that the cursors are closed.

```foxpro
oCustomer = CREATEOBJECT("busCustomer")
lnCount = oCustomer.GetCustomerList("TEntries")

* ** Register cursor to be closed
**THIS.Page.RegisterCursor("TEntries")**  

* ** Do whatever you need to do with the cursor
this.lstCustomers.DataSource = "TEntries"
this.lstCustomer.DataBind()
```