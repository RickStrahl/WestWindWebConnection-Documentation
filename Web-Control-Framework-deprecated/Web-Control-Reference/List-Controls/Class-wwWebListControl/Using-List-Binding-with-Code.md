List controls support databinding both as List binding and single DataSource binding as the following example demonstrates. The code below binds the list to a query result cursor called TStates and binds the SelectedValue to a business object property called State: 

```foxpro
* ** Use bus object to retrieve State List
this.oLookups = NEWOBJECT("wwLookups","wwDevRegistry")

* ** Retrieve TStates Cursor
lnResult = this.oLookups.GetStates()

this.txtState.FirstItemText = "* ** Please enter a State"
this.txtState.DataSource = "TStates"
this.txtState.DataTextField = "State"
this.txtState.DataValueField = "StateCode"

* ** Bind the ControlSource
* ** Effectively sets the SelectedValue from the bus obj state
this.txtState.ControlSource = "this.Page.oEntry.oData.State"

* ** Bind the input controls only on the first hit
IF !this.IsPostBack
   * ** Bind all the single field controls
   THIS.DataBind()
ENDIF
```

The DataSource binding in this example binds the Listbox with the content to display in the dropdown for list binding and binds the SelectedValue to the State field in the business object.

Note that you'll want to always bind the list, but you'll only want to bind the