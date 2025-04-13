Overridden AddControl method that allows adding Column objects to the DataGrid.

**Script Code:**  
```html
<ww:wwWebDataGrid ID="gdCustomers" runat="server">
<Columns>
    <ww:wwWebDataGridColumn runat="server" id="colCompany"  Expression="Company" HeaderText="Company"  />
    <ww:wwWebDataGridColumn runat="server" id="colCareOf" Expression="CareOf" HeaderText="Name"  />
</Columns>
</ww:wwWebDataGrid>
```

**Fox Code:**  
```foxpro
THIS.gdCustomers = CREATEOBJECT("wwwebdatagrid",THIS)
THIS.gdCustomers.Id = "gdCustomers"
THIS.AddControl(THIS.gdCustomers)

THIS.colCompany = CREATEOBJECT("wwwebdatagridcolumn",THIS)
THIS.colCompany.Id = "colCompany"
THIS.colCompany.Expression = [Company]
THIS.colCompany.HeaderText = [Company]
THIS.colCompany.UniqueId = [gdCustomers_colCompany]
THIS.gdCustomers.AddControl(THIS.colCompany)

THIS.colCareOf = CREATEOBJECT("wwwebdatagridcolumn",THIS)
THIS.colCareOf.Id = "colCareOf"
THIS.colCareOf.Expression = [CareOf]
THIS.colCareOf.HeaderText = [Name]
THIS.colCareOf.UniqueId = [gdCustomers_colCareOf]
THIS.gdCustomers.AddControl(THIS.colCareOf)
```