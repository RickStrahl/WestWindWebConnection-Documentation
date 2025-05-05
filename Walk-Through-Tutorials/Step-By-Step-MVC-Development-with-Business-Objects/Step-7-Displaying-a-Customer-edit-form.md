Now it's time to actually edit and add customers. This process basically consists of two main operations - displaying the data initially when a user accesses the page in edit mode and then accepting the changes and writing them to the database.

Here's the initial form we'll create in this step:

![](/images/stepbystep/EditCustomer_script.png)

The first step then is to be able to display the data, which is essentially the same code we used previously for displaying a customer. For now let's create a placeholder Controller method that allows us to at least display the customer information. We then add the update code later on. Here's the initial EditCustomer Controller method:

```foxpro
FUNCTION EditCustomer()

lcId = Request.Params("id")

loCustBus = CREATEOBJECT("cCustomer")
IF !loCustBus.Load(lcId)
   *** if no customer exists lets create a new one
   loCustBus.New()  
ENDIF


*** Error display handler for binding errors and messages
poError = CREATEOBJECT("HtmlErrorDisplayConfig")
poCustomer = loCustBus.oData

IF Request.IsPostBack()
   *** Code for capturing posted data goes here   
ENDIF

Response.ExpandScript()
ENDFUNC
```

Next we need to create a new Script page. Again I'll use Visual Studio:

* Select the project node
* Right click and select *Add* then *Add new Item...*
* Select *Web Connection Template Content Page*
* Set the name to `EditCustomer.ctd`

In this content page we now need to display a bunch of input fields to allow the user to edit the customer data. Each of the input controls is then bound to the property of a the customer object using the expression syntax.


```html
<% pcPageTitle = "Edit Customer - Customer Demo Sample" %>
<% Layout="~/views/_layoutpage.wcs" %>

<div class="container">
    <a href="customerlist.ctd"  class="btn btn-link pull-right"
       title="Customer List">
        <i class="fa fa-list"></i> List
    </a>
    <div class="page-header-text">
        <i class="fa fa-edit"></i> 
        Edit Customer
    </div>
    
    <%= HtmlErrorDisplay(poError) %>

    <form action="" method="POST"
          class="form-horizontal container"
          style="padding: 0 15px 30px;">

        <div class="form-group">
            <label class="col-sm-2">Company:</label>
            <div class="col-sm-7">
                <input name="Company" id="Company"                       
                       class="form-control" placeholder="Enter a company name"
                       value="<%= Request.FormOrValue("Company", poCustomer.Company) %>"                       
                       />
                <%= HtmlBindingError("Company",poError.Errors) %>
            </div>
        </div>

        <div class="form-group form-horizontal">
            <label class="col-sm-2">First Name:</label>
            <div class="col-sm-7">
                <%= HtmlTextBox("FirstName",poCustomer.FirstName,
                                [class="form-control" placeholder="Enter the first name"]) %>
                <%= HtmlBindingError("FirstName",poError.Errors) %>
            </div>
        </div>

        <div class="form-group form-horizontal">
            <label class="col-sm-2">Last Name:</label>
            <div class="col-sm-7">
                <input name="LastName" id="LastName"
                       value="<%= Request.FormOrValue("LastName",poCustomer.LastName) %>"
                       class="form-control" placeholder="Enter the last name" />
                <%= HtmlBindingError("LastName",poError.Errors) %>
            </div>
        </div>

        <div class="form-group form-horizontal">
            <label class="col-sm-2">Address:</label>
            <div class="col-sm-7">
                <textarea name="Address"
                          class="form-control"
                          rows="4"
                          placeholder="Enter the full address">
                    <%= Request.FormOrValue("Address",poCustomer.Address) %>
                </textarea>
            </div>
        </div>

        <div class="form-group form-horizontal">
            <label class="col-sm-2">Email:</label>
            <div class="col-sm-7">
                <input name="Email" value="<%= Request.FormOrValue('Email',poCustomer.Email) %>"
                       class="form-control"
                       placeholder="Enter the email address" />
                <%= HtmlBindingError("Email",poError.Errors) %>
            </div>
        </div>

        <div class="form-group form-horizontal">
            <label class="col-sm-2">Billing Rate:</label>
            <div class="col-sm-7">
                <%= HtmlTextBox("BillRate",poCustomer.BillRate,
                                'class="form-control" type="number" step="0.5" pattern="[\d,-,.,\,]"') %>
                <%= HtmlBindingError("BillRate",poError.Errors) %>
            </div>
        </div>


        <div class="form-group form-horizontal">
            <label for="Entered_Field" class="col-sm-2">Entered:</label>
            <div class="col-sm-7">
                <%= HtmlDateTextBox("Entered",poCustomer.Entered,"",0,[style="width: 250px;"]) %>
                <%= HtmlBindingError("Entered",poError.Errors) %>
            </div>
        </div>
 
        <div class="form-group">
            <div class="col-sm-2">&nbsp;</div>
            <div class="col-sm-7">
                <div>                    
                    <%= HtmlCheckbox("InActive","Inactive Customer", poCustomer.InActive) %>                    
                </div>
            </div>
        </div>        

        <hr />

        <button type="submit" name="btnSubmit" class="btn btn-primary">
            <i class="fa fa-check"></i> Save Customer
        </button>

    </form>
</div>            

<% section="headers" %>

<% endsection %>

<% section="scripts" %>

<script src="~/lib/jquery/dist/jquery.min.js"></script>

<!-- datetimepicker related scripts and styles -->
<script src="~/lib/moment/min/moment.min.js"></script>
<link href="~/lib/tempusdominus-bootstrap-4/build/css/tempusdominus-bootstrap-4.min.css" rel="stylesheet" />
<script src="~/lib/tempusdominus-bootstrap-4/build/js/tempusdominus-bootstrap-4.min.js"></script>
<script>
    $("#Entered").not("[type='date']").datetimepicker({
    	format: "MM/DD/YYYY",
    	keyBinds: { "delete": null }   // leave delete key
    });
</script>
<% endsection %>
```

Whoa - that's a lot more HTML than we've seen so far so let's explain what's going on from top to bottom:

### Error Display
The top of the page starts out with some error display options. Specifically there's a bootstrap style alert box using the [HtmlErrorDisplay](VFPS://Topic/_39213EUXL) Html Helper that can display errors using:

```html
<%= HtmlErrorDisplay(poError) %>
```

This control makes it easy to display errors and information messages in a variety of ways by setting the `Message` and `Icon` properties. 

The helper takes an [HtmlErrorDisplayConfig](VFPS://Topic/_4GJ0LR1OQ) object which we set up in the Controller code earlier and uses that to render a message **if a message is set**. Otherwise nothing displays. The controller code sets values on the poError object to display information about errors and when we've save a customer later.

Each of the input fields also uses an option `< %= HtmlBindingError() % >` helper to display binding errors when a binding or business logic error is encountered. I'll talk more about this later, but these components display error messages next to each control if an error is encountered.

### Data Binding
The bulk of assignments and HTML on this page deal with the input controls and databinding those controls. There are a number of different ways that you can bind values to input controls either using raw HTML, using HTML helpers or some Request helpers.

##### Raw HTML Binding
Raw binding looks like this:

```html
<div class="form-group">
    <label class="col-sm-2">Company:</label>
    <div class="col-sm-7">
        <input name="Company" value="<%= poCustomer.Company %>"
               class="form-control"
               placeholder="Enter a company name" />
        <%= HtmlBindingError("Company",poError.Errors) %>
    </div>
</div>
```
You simply have raw HTML and you bind the `poCustomer.Company` value into the control. This basically assumes that the model value passed is alway set and updated.

A slight variation on this theme is the better and more reliable approach of using `Request.FormOrValue()` function instead:

```html
<input name="Company" 
       value="<%= Request.FormOrValue("Company",poCustomer.Company) %>"
       class="form-control"
       placeholder="Enter a company name" />
```

`Request.FormOrValue()` retrieves the value from the POST buffer *if available* or otherwise uses the model value passed in.  This guarantees that the user always sees his posted input rather than a model value which may or may not get updated or which may in some cases not reflect the user input. For example, if you enter a numeric value and that value is invalid, the posted value would be lost withtout this function.

As a general rule if you assign value directly it's recommended you always use  `Request.FormOrValue()` as the assignment expression.

### Using HtmlTextBox and HtmlTextArea
Personally I prefer the previous syntax, because it's just HTML with the embedded expression. However, if you want more terse syntax using an integrated 'control' approach you can use various HTML helpers to render controls:

```html
<%= HtmlTextBox("BillRate",poCustomer.BillRate,[class="form-control"])  %>

<%= HtmlTextArea("Address",poCustomer.Address,
                 [class="form-control" placeholder="Enter the customer's address"])  %>
```

These helper render HTML that is the same as the raw HTML typed. It's a more terse syntax that makes it obvious that the control is handled.

### Handling Dates and HtmlDateTextBox
Date fields are always more tricky than they should be due to the fact that HTML has no standard way to handle date input and the fact that mobile devices include native date pickers. `HtmlDateTextbox` is a smart wrapper around the <a href="https://eonasdan.github.io/bootstrap-datetimepicker/" target="top">BootStrap DateTimePicker</a> JavaScript component that is smart enough to detect mobile devices and switch to using native controls on iOS and Android phones and tablets.

Using this control is a two step process.

**Embedding the DateTimePicker**

```html
<div class="form-group form-horizontal">
    <label for="Entered_Field" class="col-sm-2">Entered:</label>
    <div class="col-sm-10">
        <%= HtmlDateTextBox("Entered",poCustomer.Entered,
                           .f.,.f.,[style="width: 150px;"]) %>
        <%= HtmlBindingError("Entered",poError.Errors) %>
    </div>
</div>
```

This creates the actual date picker control output **or** a mobile device native control on mobile. 

If you'd rather use the DatePicker explicitly with HTML you can also use this:

```html
<div class="input-group" id="Entered" style="width: 150px;">
    <input id="Entered_field" name="Entered" type="text" 
           class="form-control" 
           value="<%= Request.FormDateOrValue("Entered",poCustomer.Entered) %>">
           
    <span class="input-group-addon">
        <i class="fa fa-calendar"></i>
    </span>
</div>
```

The second thing required are the script files you have to add, plus a small bit of JavaScript code to hook up code.

```html
<% section="scripts" %>

<script src="~/lib/jquery/dist/jquery.min.js"></script>

<!-- datetimepicker related scripts and styles -->
<script src="~/lib/moment/min/moment.min.js"></script>
<link href="~/lib/tempusdominus-bootstrap-4/build/css/tempusdominus-bootstrap-4.min.css" rel="stylesheet" />
<script src="~/lib/tempusdominus-bootstrap-4/build/js/tempusdominus-bootstrap-4.min.js"></script>
<script>
    $("#Entered").not("[type=date]").datetimepicker({
    	format: "MM/DD/YYYY",
    	keyBinds: { "delete": null }   // leave delete key
    });
</script>
<% endsection %>
```

The first part adds the required scripts which are jQuery, the datetimepicker itself, plus Moment.js for date formatting support. Finally there's a block of JavaScript that hooks up the date picker to the actual date control. Not the conditional `.not([type=date])` which excludes native date controls which the `HtmlDateTextBox()` generates for mobile devices.

For a date time display you'd use:

```html
<script>
    $("#Timein,#Timeout").not("[type=datetime-local]").datetimepicker({
    	format: "MM/DD/YYYY hh:mm a",
    	keyBinds: { "delete": null }   // leave delete key
    });
</script>
```

### Form and Submitting
At the bottom of the form there's a submit button that submits the page back to the server:

```html
<button type="submit" name="btnSubmit" class="btn btn-primary">
    <i class="fa fa-check"></i> Save Customer
</button>
```

The entire form and all the input fields then are wrapped into a `<form action="" method="POST">` control, which posts the submitted data to the server using the current URL. IOW, it posts back to itself.