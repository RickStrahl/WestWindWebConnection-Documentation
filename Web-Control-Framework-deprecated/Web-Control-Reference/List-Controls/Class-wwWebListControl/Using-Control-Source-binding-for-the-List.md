Control Source binding works like any other control like the TextBox, CheckBox or Label. In ControlSource binding the SelectedValue property is bound to a data source. So you can bind to any expression that can be two-way bound which typically will be a database field of an open table or cursor, or a property of a business object or really any object whatsoever.

```foxpro
this.txtState.ControlSource = this.Page.oDeveloper.oData.StateCode
```