Collection of columns to add manually add to this config.

Use the loConfig.AddColumn() method to add columns:

```foxpro
loConfig = CREATEOBJECT("HtmlRecordConfig")
loConfig.Width = "500px"

loCol = CREATEOBJECT("HtmlRecordColumn","Company")
loConfig.AddColumn(loCol)

loCol = CREATEOBJECT("HtmlRecordColumn","Entered","Customer Since")
loCol.FieldType = "T"
loConfig.AddColumn(loCol)

* ... add more columns manually as needed
? loConfig.Columns.Count  

lcHtml = HtmlRecord("tt_cust",loConfig)
```