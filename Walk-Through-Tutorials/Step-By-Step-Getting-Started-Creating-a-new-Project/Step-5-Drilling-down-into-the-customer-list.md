Now that we know how to display a basic list, let's jazz it up a little bit by adding links to the company so we can drill down into the data and display the customer detail.

Change the CustomerList SQL to:

```foxpro
SELECT HREF([ShowCustomer.wp?id=] + id,company) as Company, ;
       FirstName + LastName as Customer_Name, ;
       ShortDate(Entered,1) as Entered ;
   FROM Customers ;
   WHERE UPPER(Company) = UPPER(lcCompany) ;
   ORDER BY Company ;
   INTO CURSOR TQuery      
```

Here's what the list looks like now:

![](/images/stepbystep/CustDataLink.png)

This also makes the list look a little nicer by displaying less information, cleaning up the labels and more importantly adding hyperlinks for each of the customer entries. 

### Expressions
You can control the headers of the grid via the field names generated in the query. I combined the LastName and FirstName fields with an expression that concatenates both and then creates a Customer_Name field. The renderer automatically replaces the _ with a space.

The hyperlink for drilling into the data by going to another page is generates as a string using the HREF() helper function which takes a URL and a caption as input and generates URL. The URL generated is something like:

**<a href="http://localhost/webdemo/showcustomer.wp?id=_4fg12y7tx" target="top">http://localhost/WebDemo/ShowCustomer.wp?id=_4FG12Y7TX</a>**

which points to another page. If you click on noe of the links now you'll find that the request fails:


![](/images/stepbystep/ErrorNoMethodFound.png)

This makes perfect sense: We haven't created a `ShowCustomer` method yet. Let's create it.

### Showing the Customer entry
A new Web Connection request means a new method in the `WebProcess` class. The `ShowCustomer()` method looks like this:

```foxpro
FUNCTION ShowCustomer()
LOCAL lcId,lcHtml

lcId = Request.Params("id")

SELECT * FROM Customers ;
   WHERE id = lcId ;
   INTO CURSOR TQuery

IF _Tally < 1
   *** Create an error page
   this.ErrorMsg("Invalid Customer Id",;
   "The customer couldn't be retrieved. Make sure the URL is correct " + ;
   "and points at a valid customer record.hr/>Please return to the <a href='Customerlist.wp'>customer list</a>...")
   RETURN 
ENDIF

*** Helper to generate templated HTML Header
Response.Write(this.PageHeaderTemplate("Customer List"))


TEXT TO lcHtml TEXTMERGE NOSHOW
<a href="customerlist.wp" 
   class="btn btn-success btn-sm pull-right" 
   style="margin-top: 20px;">
	<i class="fa fa-arrow-left"></i> Customer List
</a>

<h3>
   <i class='fa fa-user'></i> <<Company>>
</h3>   

<hr />
ENDTEXT
Response.Write(lcHtml)

*** Render the Record into a table
lcHtml = HtmlRecord("TQuery")
Response.Write(lcHtml)

Response.Write(this.PageFooterTemplate())

ENDFUNC
```

The output from this display using  `HtmlRecord()` looks like this:

![](/images/stepbystep/CustDataDisplay.png)


This output is very basic and pretty rough as it just uses the field names for labels. But `HtmlRecord()`  gives you a quick and dirty way to display data from a single cursor record or object. It's a great object to use as a place holder for data that will later be added.

#### HtmlRecord Formatting
The `HtmlRecord()` function, like `HtmlDataGrid()` can take a configuration object that lets you specify exactly how data is displayed. This takes a little more code, but gives you more control.

You create an **[HtmlRecordConfig object](VFPS://Topic/_3AS14YCB5)** object and then attach individual **[HtmlRecordColumn objects](VFPS://Topic/_3AS15H1KZ)** to specify how each column is rendered.

```foxpro
loConfig = CREATEOBJECT("HtmlRecordConfig")
loConfig.Width = "700px"

*loConfig.HeaderCssClass = "col-sm-3 my-record-header"
*loConfig.ItemCssClass = "col-sm-7"

*** Create columns manually for each field
loCol = CREATEOBJECT("HtmlRecordColumn","Company")
loConfig.AddColumn(loCol)

loCol = CREATEOBJECT("HtmlRecordColumn","FirstName + ' ' + LastName","Name")
loConfig.AddColumn(loCol)

loCol = CREATEOBJECT("HtmlRecordColumn","Address")
loCol.FieldType="M"
loConfig.AddColumn(loCol)

loCol = CREATEOBJECT("HtmlRecordColumn","Email")
loConfig.AddColumn(loCol)

loCol = CREATEOBJECT("HtmlRecordColumn","BillRate","Bill Rate")
loCol.Format = "$$,$$$.99"
loCol.FieldType = "N"
loConfig.AddColumn(loCol)

loCol = CREATEOBJECT("HtmlRecordColumn",[ShortDate(Entered,2)],"Entered")
loCol.FieldType = "C"
loConfig.AddColumn(loCol)

lcHtml = HtmlRecord("TQuery",loConfig)
Response.Write(lcHtml)
```

This results in a much cleaner and more optimized view of the same data:

![](/images/stepbystep/CustDataDisplayFormattedRecord.png)

Much cleaner, eh? Using explicit fields and formatting can net you something that might just be usable as is for quick data display.


Note this display is **not actually using an HTML table**, but rather using a **responsive layout** that stacks in smaller screen dimensions so it works on mobile devices. 

A small width view looks like this:

![](/images/stepbystep/CustDataDisplaySmallStacked.png)

---
[Step 6 - Scripts and Templates and Setting up an HTML Editor](VFPS://Topic/_5TC1BNZMD)