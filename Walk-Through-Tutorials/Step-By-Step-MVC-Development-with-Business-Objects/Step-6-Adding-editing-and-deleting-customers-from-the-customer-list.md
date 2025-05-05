Now let's provide the functionality to edit, add and delete customers. 

First let's modify the list display so that we have edit and remove buttons for all items listed:

![](/images/stepbystep/CustomerList_Script_EditDelete.png)

Notice the last column that now shows buttons to edit and delete customers.

To add this feature we can simply add a new column to the HtmlDataGrid like this:

```foxpro
PRIVATE pcPage
pcPage = Request.QueryString("Page")  && capture current page so we can redisplay it

...

loColumn = CREATEOBJECT("HtmlDataGridColumn")
loColumn.Expression = ;
   "HREF([editcustomer.ctd?id=] + TCustomers.Id,[<i class='fa fa-edit' title='Edit Customer'></i>]) +;
   '&nbsp;&nbsp;' + "  +;
   "HREF([deletecustomer.ctd?id=] + TCustomers.Id + '&Page=' + pcPage,[<i class='fa fa-times' title='Delete Customer'></i>])" 
loColumn.HeaderText = " "   && no header
loGridConfig.AddColumn(loColumn)
```

### Deleting a Customer
To remove a customer we can then use a simple Controller method using the base functionality of the business object:

```foxpro
FUNCTION DeleteCustomer()
LOCAL lcId, lcPage, loCustBus

lcId = Request.QueryString("id")
lcPage = Request.QueryString("page")


loCustBus = CREATEOBJECT("cCustomer")
IF !loCustBus.Load(lcId)
   this.ErrorMsg("Invalid Customer",;
                     "This customer can't be deleted. Please make sure you select a valid customer and try again.",;
                     "text/html",;
                     3,"CustomerList.ctd")
                                          
   RETURN
ENDIF

loCustBus.Delete(lcId)

Response.Redirect("customerlist.ctd?page=" + lcPage)
ENDFUNC
```

This is pretty straightforward and simple.

One thing I'm glossing over for now is that you probably should secure editing operations so that not just anybody can access the delete (and edit operations). I'm circling back to this issue later in this tutorial. 

Next let's see how to edit a Customer.