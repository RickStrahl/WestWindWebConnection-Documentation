As we're using Visual FoxPro we're pretty much always interested in database access. Web Connection makes consuming data in the Web Controls framework fairly easy. There are two kinds of databinding mechanisms supported by the framework.

### ControlSource Binding (Single Control Binding)
Control Source binding works similar to the way it works in Visual FoxPro. You assign a ControlSource which is an expression that is used for binding and unbinding a value from a control to an expression. For two-way controls (TextBox, CheckBox etc.) the expression must be able to read and write data.  For one way controls Controlsources can be any valid expression.

The following properties and methods apply:
* **ControlSource**  
The ControlSource is an expression that is used to bind an expression - a field or property usually - to the property of a control - text usually. The control property that is bound is control with the ControlSourceProperty of the control

ControlSource Expressions must be in scope in the context of the control. This means that to reference other controls on the form you need to first reference the form and then the other controls or objects on that form. 

A typical ControlSource reference to a business object property might look like this

```foxpro
loCtl.ControlSource = "this.Page.oDeveloper.oData.Name"
```

* **ControlSourceFormat**  
If a ControlSource is set you can also set the format for this ControlSource. Formats are used only when displaying the value. Format can be either a standard FoxPro format expression or if preceeded by an = sign a UDFor function that takes a single input parameter of the value and returns a string. For example you can use:

ControlSource="oGuest.Entered" ControlSourceFormat="=this.Page.ConvertTimeString"

where ConvertTimeString is a method on the form. Any UDF that takes a single parameter and returns a string can be used. Note: This same scheme can also used in DataGrids for column binding.

* **DataBind**  
In the context of ControlSource binding DataBind causes a control to bind its ControlSource. This is an explicit operation - it doesn't happen automatically. Typically ControlSource binding is performed by calling the DataBind method of the form which then delegates DataBind to all the controls on the page causing all controls to bind.

* **UnbindData**  
UnbindData performs the opposite action of DataBind: It takes the values in the controls and stores them in to the expression specified by the ControlSource - usually a field or a property. As with DataBind this method must be called explicitly.

* **BindingErrors Collection**  
When you call UnbindData() it's possible that there are errors trying to unbind the data. For example, you might have a DateTime value and the user enters an invalid DateTime Format value which makes the unbind fail. The default unbinding mechanism catches this error and adds a BindingError object to the BindingErrors collection. 

This BindingErrors collection can be used to dispaly error indicators next to controls, and display an HTML error message. In conjunction with the wwWebErrorDisplay object it can also provide a rich display of error information. You can also add custom binding errors and there's even a method to store wwBusiness oValidationErrors directly into the BindingErrors collection for fully automated error handling.


### DataSource Binding (List Binding)
DataSource binding applies to list controls like the wwWebListBox, wwWebDropDownList, wwWebRadioButtonList and wwWebDataGrid. It involves setting the DataSource property to a VFP cursor and on some controls providing addition properties for display and value assignments.
* **DataSource**  
DataSource properties are used for list based binding and usually refer to a FoxPro cursor object. Setting the DataSource on a Listbox loads the list content with the values from the cursor.

* **ControlSource**  
ControlSource refers to a simple field binding - one control property to one value expression. So you can bind a TextBox .Text property to a field of a database or property of a business object. 

**ControlSourceFormat**  
If a ControlSource is set you can also set the format for this ControlSource. Formats are used only when displaying the value. Format can be either a standard FoxPro format expression or if preceeded by an = sign a UDFor function that takes a single input parameter of the value and returns a string. For example you can use:

ControlSource="oGuest.Entered" ControlSourceFormat="=this.Page.ConvertTimeString"

where ConvertTimeString is a method on the form. Any UDF that takes a single parameter and returns a string can be used. Note: This same scheme can also used in DataGrids for column binding.

* **DataBind**  
DataBind in List Binding is used to bind a cursor to the list content of the control. List controls usually set the DataSource Control as well as selection fields like DataValueField and DataTextField. Some controls like the wwWebDataGrid provide a richer model with column objects to specify exact formatting of the data.