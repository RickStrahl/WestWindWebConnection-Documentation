Now that we have a business object and a new process class we're ready to access data and use it on the Web.

### Startup Configuration
To use the business object we need to do a couple of admin tasks first before we can start creating data access code. We need to make sure that the `wwBusiness` and `cCustomer` classes are visible from our process class. 

To do this we need to ensure that we load the required dependencies and set the path to the data during server startup. In Web Connection you can do this in the server's overridden `OnLoad()` method. 

Open `WebDemoMain.prg` and find the `WebDemoServer::OnLoad()` method and add the following to the bottom of that method:

```foxpro
*** Add any data paths - SET DEFAULT has already occurred so this is safe!
DO PATH WITH ".\Data"    && Add data path

*** Add any SET CLASSLIB or SET PROCEDURE code here
SET PROCEDURE TO cCustomer ADDITIVE

select * from test
```
Next let's give some thought of what data tasks we want to perform. We want to retrieve a list of customers and display them. In the Core Step By Step walk through we simply used a SQL query inline as part of a process method, which is not a good idea. 

Using business objects, any access to the database should go through the business object, so regardless of how simple a query is, create a method that gets that data for you instead, using the business object and the Query() method.

Open the **cCustomer** class and add a method called `GetCustomers()`:


```foxpro
***********************************************************************
FUNCTION GetCustomers(lcCompany)
*********************************
***  Function: Returns a customer list with an optional company filter
***            into a cursor TCustomers
***    Assume:
***      Pass: lcCompany  -  optional company filter
***    Return: number of records or 0
************************************************************************

IF EMPTY(lcCompany)
   lcCompany = ""
ENDIF
lcCompany = lcCompany + "%"

*** Query parameters have to be PRIVATE so they 
*** are visible in the query processor
PRIVATE pcCompany
pcCompany = lcCompany

lcSql = "select id,company,firstname,lastname,Entered from Customers "  +;
        "where company like ?pcCompany " +;
        "order by company" 

RETURN this.Query(lcSql,"TCustomers")
ENDFUNC
*   GetCustomers
```

Why add a method for this simple SQL statement? Simple - it allows you to isolate all data access in the business object. If changes need to be made you can always come back here to do it in this single location. This becomes more important with more important query operations where you can pass parameters or property values into the method to set up for complex tasks. Using the Query method (or the lower level Execute() method) rather than a Fox SQL DML statement will allow this code to migrate to SQL Server later on.

### Displaying the Data on a Web Page
To display a customer list, let's create a method in the `CustomerProcess.prg` class called `CustomerList()`:

```foxpro
FUNCTION CustomerList()

loCustBus = CREATEOBJECT("cCustomer")
lnCount = loCustBus.GetCustomers()

IF lnCount < 1
   TEXT TO lcHtml NOSHOW TEXTMERGE
   <div class="alert alert-warning">
      There are no matching records to display.
      <hr />
      Error: <<loCustBus.cErrorMsg>>
   </div>
   ENDTEXT
ELSE
   LOCAL loGridConfig
   loGridConfig = CREATEOBJECT("HtmlDataGridConfig")
   loGridConfig.PageSize = 8  && Add paging
   lcHtml = HtmlDataGrid("TCustomers",loGridConfig)  
ENDIF

Response.Write( Process.PageHeaderTemplate("Customer List") )
Response.Write( lcHtml )
Response.Write( Process.PageFooterTemplate() )

ENDFUNC
*   CustomerList
```
The result from this simple bit of code is:

![](/images/stepbystep/CustomerList.png)

And voila - you have your very first request that is served using a business object. 

Not super impressive since all we're doing is querying data, but it's a first step to validate the concept. Notice that the Controller/Process Method code is very short because the business logic (the query and filtering logic) lives in a separate class method thus abstracting the logic into something that's easy to understand.



As in the first Step By Step Guide I'm relying on the `wwProcess::PageHeaderTemplate()` and `wwProcess::wwPageFooterTemplate()` methods to provide the basic page 'wrapper' layout for the header and footer. I customized the `~\views\_PageHeaderTemplate.html` and `~\views\_PageFooterTemplate.html` slightly by changing the header and copyright company.

### Filtering the List by Company
The whole list is cool but to filter we need to do a little more work. Let's allow fltering by company. Change the method as follows:

```foxpro
FUNCTION CustomerList()
LOCAL lcCompany

#IF .F. 
LOCAL Request as wwRequest, Response as wwResponse, Server as wwServer, Process as wwProcess
#ENDIF

*** Look for Company key in Form/QueryString/Session
lcCompany = Request.Params("Company")

loCustBus = CREATEOBJECT("cCustomer")
lnCount = loCustBus.GetCustomers(lcCompany)

IF lnCount < 1
   TEXT TO lcHtml NOSHOW TEXTMERGE
   <div class="alert alert-warning">
      There are no matching records to display.
      <hr />
      Error: <<loCustBus.cErrorMsg>>
   </div>
   ENDTEXT
ELSE
   LOCAL loGridConfig
   loGridConfig = CREATEOBJECT("HtmlDataGridConfig")
   loGridConfig.PageSize = 8  && Add paging
   lcHtml = HtmlDataGrid("TCustomers",loGridConfig)  
ENDIF

Response.Write( Process.PageHeaderTemplate("Customer List") )
Response.Write( lcHtml )
Response.Write( Process.PageFooterTemplate() )

ENDFUNC

<pre>FUNCTION ShowCustomers()

*** Retrieve the country entered
lcCountry = Request.QueryString("Country")
IF EMPTY(lcCountry)
   lcCountry = Request.Form("Country")
ENDIF

*** We have to persist the Country in order for paging to work
THIS.InitSession()
IF EMPTY(lcCountry)
   lcCountry = THIS.oSession.GetSessionVar("ShowCursor_Country")
ELSE
   THIS.oSession.SetSessionVar("ShowCursor_Country",lcCountry)
ENDIF


loDev = CREATEOBJECT("cDeveloper")
lnCount = loDev.DeveloperList(lcCountry)

IF lnCount < 1
   THIS.ErrorMsg("No matches","No developers match your query")
   RETURN
ENDIF

loSC = CREATEOBJECT("wwShowCursor")
loSC.lAlternateRows = .T.

*** Size each page to 10 items
loSC.nPage_ItemsPerPage = 7
loSC.cPage_PageUrl = "ShowDevelopers.dp?"

loSC.ShowCursor()

Response.HTMLHeader("Developer List")

*** Display Country dialog
Response.FormHeader("ShowDevelopers.dp")
Response.Write("Country: ")
Response.FormTextBox("Country",lcCountry,20)
Response.FormButton("btnSubmit","Go")
Response.Write("</form><hr>")

Response.Write( loSC.GetOutput() )

Response.HTMLFooter(PAGEFOOT)

ENDFUNC
*  DevProcess :: ShowDevelopers
```

The result now looks like this:

![](IMAGES\STEPBYSTEP\DEVELOPERLISTCOUNTRY.GIF)