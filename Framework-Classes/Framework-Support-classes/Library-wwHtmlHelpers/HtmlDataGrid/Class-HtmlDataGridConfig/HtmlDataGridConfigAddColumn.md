Adds an HtmlDataGridColumn to the Columns collection.

#### Examples
Add a column object:

```foxpro
loCol = CREATEOBJECT("HtmlDataGridColumn","Entered","Date")
loCol.FieldType = "T"
loCol.ItemAttributeString = [style="font-weight: bold"]
loConfig.AddColumn(loCol)
```

Add a column by expression to evaluate and header text:

```foxpro
loConfig.AddColumn("CareOf","Company","C")
```