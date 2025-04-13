At this point we've created a code based implementation, meaning we've implemented both our controller code and view display using code. While this works fine for simple page logic, this mixing of code and HTML display logic gets to be a mess very quickly.

Web Connection supports an **MVC** (Model View Controller) workflow by way of creating a **Model** via Business Object that provide business logic, **Controller** via wwProcess methods that provide logic, and **View** via [HTML Scripts or Templates](VFPS://Topic/_4DB0VHAQW) that mix HTML and FoxPro expressions and FoxPro control code constructs.

### Using Scripts to separate Controller and Views
So let's rewrite the previous customer list example by using a controller method that defers retrieving data to the business object (the model) and handles the HTML rendered by rendering an HTML Script page. The controller then just coordinates between the two to get the data ready for passing to the View.

### Controller Method Code
We'll start with the Controller code inside of the Process method and removing most of the HTML generation code out of the Controller method:

```foxpro
FUNCTION CustomerList()

*** Vars that are passed for processing in script
*** should be marked as PRIVATE
PRIVATE pcCompany, pnCustomerCount, pcGridHtml

*** Look for Company key in Form/QueryString/Session
pcCompany = Request.Params("Company")

loCustBus = CREATEOBJECT("cCustomer")
pnCustomerCount = loCustBus.GetCustomers(pcCompany)

pcGridHtml = ""
IF pnCustomerCount > 0
   LOCAL loGridConfig
   loGridConfig = CREATEOBJECT("HtmlDataGridConfig")
   loGridConfig.PageSize = 6  && Add paging
   
   LOCAL loColumn as HtmlDataGridColumn
   loColumn = CREATEOBJECT("HtmlDataGridColumn")
   loColumn.Expression = "Href([customer.ctd?id=] + TCustomers.Id,TCustomers.Company)"
   loColumn.HeaderText = "Company"
   loGridConfig.AddColumn(loColumn)

   loColumn = CREATEOBJECT("HtmlDataGridColumn")
   loColumn.Expression = "FirstName + ' ' + LastName"
   loColumn.HeaderText = "Name"
   loGridConfig.AddColumn(loColumn)

   loColumn = CREATEOBJECT("HtmlDataGridColumn")
   loColumn.Expression = "ShortDate(Entered,1)"   
   loColumn.HeaderText = "Entered On"
   loGridConfig.AddColumn(loColumn)
   
   pcGridHtml = HtmlDataGrid("TCustomers",loGridConfig)   
ENDIF

Response.ExpandScript()

*** The above is the same here as:
* Response.ExpandScript("~\customerlist.ctd")
ENDFUNC
```

As you can see the code is pretty short other than the DataGrid configuration which specifically formats various columns explicitly as they use custom expressions. Otherwise the code is not that different than the first example except notably there's no direct HTML generation code and Response.Write() code. Instead we're deferring that in the call to render the View with `ExpandScript()`.

The code breaks out the HtmlDataGrid generation code by explicitly specifying and formatting the fields displayed, adding a link to the Customer page as a field value and adding paging.  

There are a few enhancements in this code: Links are generated for each company entry using the HREF() function as an expresison:

```foxpro
loColumn.Expression = "Href([customer.ctd?id=] + TCustomers.Id,TCustomers.Company)"
```

The name combines *firstname* and *lastname* and the date is formatted using the `ShortDate()` function. You can use any FoxPro expression as a column expression.

Several values are captured into **PRIVATE** vars: pcCompany, pnCustomerCount and the pcGridHtml. These captured **PRIVATE** vars are then embedded into the View in the next step.

### Creating the CustomerList View as a Script Page
I'm going to use Visual Studio to add a new Html Script to our Web files, because Web Connection provides a number of pre-configured templates that make short work of creating complete pages without having to repeat yourself.

> #### @icon-info Visual Studio is not required but recommended
>The following examples use Visual Studio to add new script pages to your HTML project. Web Connection ships with a number of Visual Studio Page templates that facilitate setting up new pages. While this is highly productive, it's not a requirement - you can create you page layouts using any editor manually if you choose or simply cut and paste from the templates which live in `<wcInstall>\VisualStudio\PageTemplates`. It's easy to cut and paste from these templates into any editor or just manually create your script pages.

So let's jump over into Visual Studio and open the new project.

* Open Visual Studio File|Open|Web Site
* Select \WebConnectionProjects\WebDemo\CustomerDemo

Right click on the CustomerDemo Project node and choose *Add* | *Add New Item...* from the context menu:

![](IMAGES/stepbystep/AddNewScript.png)

Then make sure you choose *Visual CSharp* as the language and choose a template. In this case we're going to add a *Web Connection Template Content Page* which is a content page that uses a common Layout page to handle the base page layout and provides a simple content area the page created.

![](IMAGES/stepbystep/AddContentTemplate.png)

Because the generated page is a 'content' page, it's rather simple and contains only an HTML fragment into which you can add your content:

```html
<% pcPageTitle = "CustomerList" %>
<% Layout="~/views/_layoutpage.wcs" %>

<div class="container">
    <div class="page-header-text">
        <i class="fa fa-list-alt"></i> 
        CustomerList
    </div>

    put your content here...
</div>            

<!-- remove sections if you're not using them -->
<% section="headers" %>

<% endsection %>

<% section="scripts" %>

<% endsection %>
```

Sections allow you to push content from the content page into the Layout page whereever the Layoutpage defines `< %= RenderSection() % >` directives. In this example we won't be using this feature so I'll remove them in the following examples for brevity.

Let's add the HTML to handle the display of our Customer List as well as adding a form field that allows us to filter the list by Company name:

```html
<% pcPageTitle = "Customer List - Customer Demo Sample" %>
<% Layout="~/views/_layoutpage.wcs" %>

<div class="container">
    <div class="page-header-text">
        <i class="fa fa-list-alt"></i> 
        Customer List <span class="b"></span>
        <span class="badge"><%= pnCustomerCount %></span>
    </div>    

    <form action="" method="POST">
        <div class="input-group" style='margin: 20px 0;'>
          <span class="input-group-addon">
              <i class="fa fa-user"></i>
              Company:
          </span>
          <input type="text" name="Company" 
                 class="form-control" 
                 style="width: 200px;" 
                 value="<%= Request.FormOrValue("Company",pcCompany) %>"
                 />&nbsp;
  
             <button type="submit" name="btnSearch" class="btn btn-default" type="button">
             Search...
          </button>	
        </div>

    </form>
    <div class="container">
        <% if pnCustomerCount < 1 %>
        <div class="alert alert-warning">
            <i class="fa fa-warning"></i>
            There are no matching customers to display.        
        </div>
        <% else %>
        <%= pcGridHtml %>
        <% endif %>
    </div>
</div>             
```

This page adds HTML with a few FoxPro expressions delimited with `< %= expression % >`  blocks for code and code snippets.

Here's what the page now looks like:

![](IMAGES/stepbystep/CustomerList_Script.png)

Notice the links generated from the `HREF()` expression. The links are currently non-functional because we don't have a `Customer.ctd` page yet - we'll create that in the next step.