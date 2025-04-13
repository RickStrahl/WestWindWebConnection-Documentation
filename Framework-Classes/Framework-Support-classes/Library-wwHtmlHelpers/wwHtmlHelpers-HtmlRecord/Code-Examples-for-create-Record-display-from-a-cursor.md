The following examples all use Web Connection templates (.wc files) to demonstrate examples. They use a single code block and write out the result from the HtmlRecord() operation.

The examples each show the complete template page. Please realize that you can use HtmlRecord also in Script pages (.wcs), Web Control Pages (.wcsx) or even in plain process class methods. 

The examples are chosen because they are easy to test yourself: 
* Copy the content into a page with .wc extension
* Copy the Page into your Web Connection application virtual
* Execute the page like
http://localhost/wconnect/HtmlRecord.wc?id=32

### Display all fields in the active record
Create a record display for all fields in the current record (Template code):

```foxpro
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <link href="westwind.css" rel="stylesheet" type="text/css" />
    </style>
</head>
<body>
<h1>HtmlRecord Sample</h1>

<%
IF !USED("tt_cust")
   USE tt_cust IN 0
ENDIF
SELECT tt_cust

* **Find a specific record 
lcId = Request.QueryString("id")
LOCATE FOR Pk = val(lcId)

* **Create the Html table (RETURN outputs into Page)
RETURN HtmlRecord("tt_Cust")
%>

</body>
</html>
```

### Explicitly add Columns
The following example is much like the previous one, but rather than auto-generating the columns from the current table structure the code explicitly generates each column and adds it manually. You get a lot of control over how each field renders by specifying a FoxPro expression, the header text etc.:

```foxpro
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <link href="westwind.css" rel="stylesheet" type="text/css" />
</head>
<body>
<h1>HtmlRecord Sample</h1>

<%
* **open a table to display a record from
IF !USED("tt_cust")
   USE tt_cust IN 0
ENDIF
SELECT tt_cust

* **Find a specific record 
lcId = Request.QueryString("id")
LOCATE FOR Pk = val(lcId)

* **Create a configuration objec
loConfig = CREATEOBJECT("HtmlRecordConfig")
loConfig.Width = "700px"

* **Create columns manually for each field
loCol = CREATEOBJECT("HtmlRecordColumn","Company")
loConfig.AddColumn(loCol)

loCol = CREATEOBJECT("HtmlRecordColumn","CareOf","Name")
loConfig.AddColumn(loCol)

loCol = CREATEOBJECT("HtmlRecordColumn",[ShortDate(Entered,2)],"Entered")
loCol.FieldType = "C"
loConfig.AddColumn(loCol)

* **now create the HTML (return value outputs)
RETURN   HtmlRecord("tt_Cust",loConfig)
%>

</body>
</html>
```

### Displaying Data from an Object
You can also display data from an object rather than from a database record. Realistically if you add columns manually you can bind to any expressions in a column - the object passed doesn't really matter - but it must be passed - actually as the expressions 

Note an important point: Anything you bind to must be scoped PRIVATE or PUBLIC so that HtmlRecord can see the expression value.

```foxpro
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <link href="westwind.css" rel="stylesheet" type="text/css" />
    <link href="scripts/themes/redmond/jquery-ui.css" rel="stylesheet" type="text/css" />    

    <script src="scripts/jquery.min.js" type="text/javascript"></script>
    <script src="scripts/jquery-ui.min.js" type="text/javascript"></script>
    <script src="scripts/ww.jquery.js" type="text/javascript"></script>
    
    <style>
        .blackborder input, .blackborder textarea
        {
            width: 500px;
        }
        .blackborder .hasDatepicker
        {
            width: 85px;
        }
    </style>
</head>
<body>
<h1>HtmlRecord Sample</h1>

<%
* **open a table to display a record from
IF !USED("tt_cust")
   USE tt_cust IN 0
ENDIF
SELECT tt_cust

* **Find a specific record 
lcId = Request.QueryString("id")
LOCATE FOR Pk = val(lcId)

* **Note that the data passed must be private!
PRIVATE poData 
SCATTER NAME poData MEMO

* **Create a configuration objec
loConfig = CREATEOBJECT("HtmlRecordConfig")
loConfig.Width = "700px"

* **Create columns manually for each field
loCol = CREATEOBJECT("HtmlRecordColumn","poData.Company","Company")
loConfig.AddColumn(loCol)

loCol = CREATEOBJECT("HtmlRecordColumn","poData.CareOf","Name")
loConfig.AddColumn(loCol)

loCol = CREATEOBJECT("HtmlRecordColumn",[ShortDate(poData.Entered,2)],"Entered")
loCol.FieldType = "C"
loConfig.AddColumn(loCol)

* **now create the HTML (return value outputs)
RETURN   HtmlRecord(poData,loConfig)
%>

</body>
</html>
```