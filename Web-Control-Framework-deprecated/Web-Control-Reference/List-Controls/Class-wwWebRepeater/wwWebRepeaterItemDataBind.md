Event that is fired when an each item is databound in the ItemTemplate.

This method actually fires after general databinding (if any) for the template has been performed and gives the opportunity to override the value with a custom value.

Here's a contrived example that binds the DateTime to a label in the ItemTemplate:

```foxpro
FUNCTION GuestList_ItemDataBind()
* ** Current Template and lblName Item in the template
loTemplate = this.GuestList.ItemTemplate

* ** Now get the control 
loCtl = loTemplate.FindControl("lblDate")

* ** Do whatever you need to with it
loCtl.Text = TRANS(DateTime())

* ** OR Databind it if you didn't data bind the list 
* oCtl.DataBind()

ENDFUNC
```