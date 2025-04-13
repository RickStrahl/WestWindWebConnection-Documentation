The Expression used to evaluate the column display value.

This value can be any FoxPro expression including a Cursor/Table Fieldname, variable, object property, UDF() or class method call.

**Examples:**  
<ul>
* TQuery.FirstName
* Upper(FirstName)
* this.Page.FormatFirstName(FirstName)
</ul>

```html
<Columns>
    <ww:wwWebDataGridColumn ID="wwWebDataGridColumn1" runat="server" 
        Expression="Company" />

    <ww:wwWebDataGridColumn ID="wwWebDataGridColumn2" runat="server" 
            Expression="FirstName + ' ' + Lastname" 
            HeaderText="Name" />

    <ww:wwWebDataGridColumn ID="colEntered" runat="server" 
        Expression="this.Page.EnteredColumnExpression(Entered)"
        FieldType="C" 
        HeaderText="Entered" />

</Columns>
```