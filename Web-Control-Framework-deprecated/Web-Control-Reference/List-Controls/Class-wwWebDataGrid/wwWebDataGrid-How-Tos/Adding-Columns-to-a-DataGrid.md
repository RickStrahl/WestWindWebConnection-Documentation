The wwWebDataGrid can render columns either automatically - rendering basically all the columns in a cursor/table that is hooked up via DataSource, or allow you to create columns individually by adding Column objects to the DataGrid. The latter can be done visually in the Visual Studio editor, via script tags, or via code.

### AutoGenerateColumns
AutoGenerateColumns can be used to force the DataGrid to render all columns of the data source automatically. In this configuration you have very little control as Web Connection uses default formatting for everything. 

This mode sets up columns in the call to DataBind. At that time the Column objects are created and added to the DataGrid.

One trick with AutoGenerateColumns is to call DataBind(), then remove or adjust column the auto generated columns. To do this call DataBind, then use things like this:

```foxpro
this.gdGrid.DataSource = "TQuery"
this.gdGrid.DataBind()

* ** Now adjust columns
this.gdGrid.Columns.Remove("FieldName")
loCol = this.gdGrid.Columns.Item("FieldName2")
loCol.HeaderText = "New heading"
loCol.Expression = "Upper(Company)"
```

### Manual Column Configuration
In most cases you'll want to explicitly configure columns, either visually or in code. The best way to do this is inside of the Visual Studio Editor. To do so select the DataGrid in design view, the right click Properties and select the Columns property in the Property Grid:

![](IMAGES%5CWebControls%5CVsConfig%5CDataGridColumnSelection_1.png)

then you can edit the columns in the Column Collection Editor:

![](IMAGES%5CWebControls%5CVsConfig%5CDataGridColumnSelection_2.png)

Each column has a number of custom properties that determine how data is bound to the column and how the column displays. The most important properties are:

**Expression**  
The binding expression. This expression is evaluated for every row that is displayed in the DataGrid. This can be a field name, a property of an object such as a business object (ie. this.Page.oMyBusiness.oData.Company) or a UDF() (upper(Company)) or a method of an object.

**Format**  
The FoxPro format expression that is used to format the field display. Can also be a UDF, function or method by prefixing with an = sign (ie. =upper or =this.Page.FormatUpdate).

**ItemAttributeString**  
This is a catchall HTML tag string that can be attached to each item. For example, you can use align="center" or style="width:200px" or class="HighLightColumn".

There are other properties of course, but these are likely the ones you'll be using most frequently.

The column object is very flexible - between the Expression and format properties you have the capability to very finely tune the output values displayed in the grid as well as providing full formatting control over the columns as they are rendered.

One very useful trick is to use Expression values that call a method on the form and then use the form method to do sophisticated formatting of the value and the cell that it displays in by manipulating the column object on the fly.

The following example demonstrates how to display a DateTime Value and highlight every value displayed within the last year. The expression would be:

Expression="this.Page.FormatUpdated( Updated )"

Note this.Page (this refers to the DataGrid). The code to handle this then manipulates the column for every access:

```foxpro
FUNCTION FormatUpdated(ltUpdated)
LOCAL loCol

* ** If HighLight Checkbox is set - highligh recent dates
IF THIS.chkHighLight.Checked
   * ** Grab the column by the ID
   loCol = this.gdDeveloperList.Columns.Item("wwWebDataGridColumn3")

   * ** And change the columnns style
   IF ltUpdated > TTOD( DATETIME() ) - 365
      loCol.ItemAttributeString=" style='background:red;color:cornsilk;font-weight:bold;' "
   ELSE
      * ** Normal colors - simply clear the style - note if Style was set on the
      * ** the column you'd have to set this value the set style
      loCol.ItemAttributeString = " style='' "
   ENDIF
ENDIF

* ** Now format the Time into a string with wwUtils TimeToC
RETURN TimeToC( ltUpdated)
ENDIF

ENDFUNC
```

Note that this code retrieves the column, and changes its style. Make sure if you change column properties that you set them on every pass! A change made here will affect all subsequent renderings.

This is very powerful, but more importantly relatively easy to do. It takes very little code to perform this sort of formatting in your FoxPro code.

### Columns in Code
Columns can also be added in code. In fact, even if you create columns in the visual editor or script code they are eventually translated into code by the Page Parser. You can of course write that code yourself. Here's an example of a DataGrid setup in WCSX script:

```html
<ww:wwWebDataGrid ID="gdDeveloperList" runat="server" CssClass="blackborder" PageSize="10" Width="640"> 
    <Columns>
        <ww:wwWebDataGridColumn ID="wwWebDataGridColumn1" runat="server" 
                                Expression="Href('EditData_Cursor.wcsx?id=' + TRANS(pk),Company )"
                                HeaderText="Company" 
                                Sortable="True" 
                                SortExpression="upper(Company)"
                                ItemAttributeString="style='width:250;'">
        </ww:wwWebDataGridColumn>
        <ww:wwWebDataGridColumn ID="wwWebDataGridColumn2" runat="server" 
                    Expression="Name"
                    HeaderText="Developer Name" 
                    Sortable="True" 
                    SortExpression="upper(name)" 
                    ItemAttributeString="style='width:200;'">
        </ww:wwWebDataGridColumn>
        <ww:wwWebDataGridColumn ID="wwWebDataGridColumn3" runat="server" 
                    Expression="Updated"
                    Format="=this.Page.FormatUpdated" 
                    HeaderText="Last Update" 
                    HeaderAttributeString="align='center'">
        </ww:wwWebDataGridColumn>
    </Columns>
</ww:wwWebDataGrid>
```

and here is the generated code for that script code:

```foxpro
THIS.gdDeveloperList = CREATEOBJECT("wwwebdatagrid",THIS,"gdDeveloperList")
THIS.gdDeveloperList.CssClass = [blackborder]
THIS.gdDeveloperList.PageSize = 10
THIS.gdDeveloperList.Width = [640]
THIS.AddControl(THIS.gdDeveloperList)


THIS.wwWebDataGridColumn1 = CREATEOBJECT("wwwebdatagridcolumn",THIS,"wwWebDataGridColumn1")
THIS.wwWebDataGridColumn1.Expression = [Href('EditData_Cursor.wcsx?id=' + TRANS(pk),Company )]
THIS.wwWebDataGridColumn1.HeaderText = [Company]
THIS.wwWebDataGridColumn1.Sortable = .T.
THIS.wwWebDataGridColumn1.SortExpression = [upper(Company)]
THIS.wwWebDataGridColumn1.ItemAttributeString = [style='width:250;']
THIS.wwWebDataGridColumn1.UniqueId = [gdDeveloperList_wwWebDataGridColumn1]
THIS.gdDeveloperList.AddControl(THIS.wwWebDataGridColumn1)


THIS.wwWebDataGridColumn2 = CREATEOBJECT("wwwebdatagridcolumn",THIS,"wwWebDataGridColumn2")
THIS.wwWebDataGridColumn2.Expression = [Name]
THIS.wwWebDataGridColumn2.HeaderText = [Developer Name]
THIS.wwWebDataGridColumn2.Sortable = .T.
THIS.wwWebDataGridColumn2.SortExpression = [upper(name)]
THIS.wwWebDataGridColumn2.ItemAttributeString = [style='width:200;']
THIS.wwWebDataGridColumn2.UniqueId = [gdDeveloperList_wwWebDataGridColumn2]
THIS.gdDeveloperList.AddControl(THIS.wwWebDataGridColumn2)


THIS.wwWebDataGridColumn3 = CREATEOBJECT("wwwebdatagridcolumn",THIS,"wwWebDataGridColumn3")
THIS.wwWebDataGridColumn3.Expression = [Updated]
THIS.wwWebDataGridColumn3.Format = [=this.Page.FormatUpdated]
THIS.wwWebDataGridColumn3.HeaderText = [Last Update]
THIS.wwWebDataGridColumn3.HeaderAttributeString = [align='center']
THIS.wwWebDataGridColumn3.UniqueId = [gdDeveloperList_wwWebDataGridColumn3]
THIS.gdDeveloperList.AddControl(THIS.wwWebDataGridColumn3)
```

As you can see there's a 1 to 1 mapping between the script code and the FoxPro code and you can use the FoxPro code directly inside of a page to add a datagrid dynamically or add additional columns to a grid.