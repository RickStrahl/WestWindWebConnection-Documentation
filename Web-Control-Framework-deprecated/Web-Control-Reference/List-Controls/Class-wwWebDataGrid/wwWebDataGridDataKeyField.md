When specified causes each row to be rendered with an id expression that includes the evaluated expression. This can be useful for client side script code to pass state information to the server for data that needs updating.

The id is generated as follows:

<DataGridId>_<EvaluatedExpression>

For example, assuming a table has an PK field you might have:

```html
<ww:wwWebDataGrid ID="gdDeveloperList" runat="server" DataKeyField="Pk">
```

Which renders each row as:

```html
<tr id="gdDeveloperList_474"  valign="top" >
```

You can then use client code from a cell to determine the value via JavaScript:
```html
<ww:wwWebDataGridColumn ID="wwWebDataGridColumn1" runat="server" 
	Expression="Href([BasicDataBinding.wcsx?id=] + TRANS(pk),Company,[ target='_ShowDev' ] )"
	HeaderText="Company" 
	ItemAttributeString="style='width:250;'" 
	**onclick="alert( 'Row Pk: ' + this.parentNode.id.split('_' )[1]);"**
```