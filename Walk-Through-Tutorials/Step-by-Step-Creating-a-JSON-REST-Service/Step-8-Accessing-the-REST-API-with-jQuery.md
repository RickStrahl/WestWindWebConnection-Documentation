If you want to access your REST API from JavaScript, you can do so quite easily using jQuery or even more easily using the ww.jquery.js library that ships with Web Connection. 

In this example, I'll use Visual Studio to create the boiler plate HTML page, but you can create the HTML by hand or copy the HTML from the templates in `Fox\VisualStudio\PageTemplates` folder into your application folder.

Lets create a simple example application that displays a drop down list of customers, allows selection of a customer and saving updated customer data back to the server. Here's what the finished result will look like:

![](/images/stepbystep/CustomerDemojQuery.png)

### Creating a new Customer List HTML Page
Let's start by creating a plain HTML page that uses jQuery. In Visual Studio I'll use the AngularJS template as that creates a pure HTML page that references all the things needed. We won't use the Angular part until the next step but it's a good starting point for this example.

To start:

* Right click on the CustomerDemo Project Node
* Select Add New Item (or press *Ctrl-N*)
* Select **Web Connection Html + Angular Page**
* Name it CustomerDemojQuery.html

The dialog should look like this:

![](/images/stepbystep/jQuerySamplePageTemplate.png)

This generates a page called `CustomerDemojQuery.htm` with a matching `CustomerDemojQuery.js` page that is automatically referenced by the Html page:

```html
<script src="./lib/jquery/dist/jquery.min.js"></script>
<script src="./lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>  

<!-- added for pop up messages -->
<script src="./lib/toastr/toastr.min.js"></script>

<!-- added for AJAX, list binding, and templates -->
<script src="./scripts/ww.jquery.min.js"></script>

<!-- our application javascript code lives in this file -->
<script src="CustomerDemojQuery.js"></script>
<script>
    $(".slide-menu-toggle-open,.slide-menu-toggle-close," +
      ".slide-menu a, #SamplesLink")
    	.click(function () {
        	$(".slide-menu").toggleClass("active");
    	});
</script>
```    

Note that I removed the `angular.min.js` reference since we're not using it in this step. I also added a reference to `ww.jquery.js` which we'll use to simplify databinding and AJAX calls.

### Create a DropDownList to hold Customer Data
Next, let's create the HTML layout for the button and listbox. 

#### Creating the base HTML
I'll add the button to load the customer list as well as the dropdown list to display the list of customers. We'll place the HTML inside of the `MainView` tag of the generated page:

```html
<div id="MainView">
    <div class="container">

        <div class="page-header-text">
            <i class="fa fa-list-alt"></i> 
            Customer Demo
        </div>

        <!-- button and select list below -->
        <button id="btnGetCustomerList" class="btn btn-primary">
            <i class="fa fa-download"></i>
            Load Customers
        </button>

        <hr />

        <div class="form-group">
            <label class="control-label" for="form-group-input">Customers</label>
            <select id="CustomerList" class="form-control"></select>

            <hr/>

            <div id="CustomerContainer" class="customer-entry" style="display: none">
            
            </div>
        </div>
</div>
```

#### Set up the external Script Page
Now let's hook up the code to populate the list box when you click on the button. To do this open the `customerDemojQuery.js` file and type out the following:

```javascript
(function () {
    var page = {
        getCustomers: function getCustomerList() {
            $.getJSON("customers.csvc")
                .success(function(custList) {
                    page.customers = custList;

                    toastr.success("Customers downloaded.");

                    $("#CustomerList")
                        .listSetData(custList, { dataTextField: "company", dataValueField: "id" })
                        .triggerHandler("change"); //  force detail to update


                })
                .error(function(parm1, parm2, parm3) {
                    toastr.error("Customer List was not retrieved.");
                });
        },
        initialize: function() {
            // Hook up event handlers
            $("#btnGetCustomerList").click(page.getCustomers);
        }
    };

    page.initialize();
})();
```

Let's take a closer look. This code:

* Creates a single object that holds page logic
* Initialize() hooks up event handlers
* getCustomers() loads customers and loads them into the list


The initialization code at this point only hooks up the event handler for the button, so when we click the `page.getCustomers()` function is fired in response.

`.getCustomers()` then uses the standard jQuery `.getJSON()` method to retrieve the result from the `customers.csvc` endpoint in our REST service. If you recall that link returns a customer list of all customers, or optionally a filtered customerlist. 

This code:

```javascript
 $.getJSON("customers.csvc")
                .success(function(custList) {
                    page.customers = custList;

                    toastr.success("Customers downloaded.");

                    $("#CustomerList")
                        .listSetData(custList, { dataTextField: "company", dataValueField: "id" })
                        .triggerHandler("change"); //  force detail to update


                })
                .error(function(parm1, parm2, parm3) {
                    toastr.error("Customer List was not retrieved.");
                });
```                

calls the HTTP endpoint and returns the customer list as an object to the JavaScript code. The code then goes on to bind the customer list data to the HTML drop downlist using a helper from **ww.jquery.js**:

```javascript
$("#CustomerList")
    .listSetData(custList, {
                dataTextField: "company",
                dataValueField: "id" 
            })
```

The `.listSetData()` function maps the **company** field to the display text and the **id** field to the option value, which is much easier than the manual code of adding `option` objects to the list explicitly.

If you try this out, you should now be able to click the button and the drop down list should populate.

### Loading an individual Customer on Selection with a Client Template
Now let's handle the selection of a customer when we select a value in the list. To do this we need:

* Hook up a `change` handler to the list
* Create a method to retrieve the customer from the server
* Display the updated customer information

#### Hook up the Change Handler
To hook up the change handler add:

```javascript
$("#CustomerList").change(page.getCustomer);
```

to the `.initialize()` method. 

#### Get the Customer Data
Then create the `.getCustomer()` method on the object:

```javascript
getCustomer: function getCustomer() {
    var id = $("#CustomerList").val();
    if (!id) {
        toastr.warning("Please select a customer.");
        return null;
    }
    
    return $.getJSON("customer.csvc?id=" + id)
        .success(function(cust) {                    
            page.activeCustomer = cust;
            toastr.success("Customer loaded:<br/> " + cust.company);
    
            // loaded from <script type="text/template">
            var template = $("#CustomerTemplate").html();
            // merge template with data
            var html = parseTemplate(template, cust);
    
            $("#CustomerContainer").html(html).show();
        })
        .error(function(err) {
            toastr.error("Customer was not retrieved.");
        });
},
```

This code starts by retrieving the CustomerList selection from the list box with:

```javascript
var id = $("#CustomerList").val();
```
You can then use this value to get the customer information by calling the **customer.csvc** link in our REST server which returns data for a single record of the Customer table.

The `.success` function then uses small template built into ww.jquery.js using the `parseTemplate()` function to merge an HTML template from the page to merge the customer values into the template.

On the HTML side add the following to the page below the drop down list:

```html
<div class="form-group">
    <label class="control-label" for="form-group-input">Customers</label>
    <select id="CustomerList" class="form-control"></select>

    <hr/>

    <div id="CustomerContainer" class="customer-entry" style="display: none">
    
    </div>
</div>


<script id="CustomerTemplate" type="text/template">
    <div class="form-group">
        <label class="control-label" for="form-group-input">Name</label>
        <div class="responsive-container">
            <input id="firstName" type="text" class="form-control" id="form-group-input" placeholder="First Name" value="<#= firstName #>" />
            <input id="lastName" type="text" class="form-control" id="form-group-input" placeholder="Last Name" value="<#= lastName #>">
        </div>
    </div>
    <div class="form-group">
        <label class="control-label" for="form-group-input">Company</label>
        <input id="company" type="text" class="form-control" id="form-group-input" placeholder="Company" 
               value="<#= company #>"
               />
    </div>
    <div class="form-group">
        <label class="control-label" for="form-group-input">Address</label>
        <textarea id="address" type="text" class="form-control" id="form-group-input" placeholder="Address"><#= address #></textarea>
    </div>
    
    <div class="margin-top">
        <button id="btnSaveCustomer" class="btn btn-primary" type="button">
            <i class="fa fa-check" style="color: lightgreen"></i>
            Save Customer info
        </button>
    </div>
</script>
```

At the top there's the empty `CustomerContainer` element, which is the empty placeholder where the customer data will be displayed. There's nothing to show initially.

Below, in the `<script>` tag is the the template that is used to merge the customer data and produce the final HTML holding the textboxes and labels:

```javascript
// loaded from <script type="text/template">
var template = $("#CustomerTemplate").html();
// merge template with data
var html = parseTemplate(template, cust);
```

The template is made up of HTML plus expressions using `<#= expression #>` expressions that are expanded into the template by the template parser. For example `<#= lastName #>` binds of the customer lastName field. Templates support expressions as above, or code blocks using `<# code #>`. Codeblocks are useful to script conditional expressions or looping structures using JavaScript code for 'scripting'. Check out the [docs for parseTemplate](VFPS://Topic/_4is1f927r) for more info.

When it's all said and done you should see customer data show up in the textboxes of the page below the dropdown based on this template expansion.

#### Initial loading of a Customer
One more little tweak you might want to make is when you initially load the customers into the drop down list you might want to automatically expand the list. The default `change` event behavior only fires when you actually change a value, so that doesn't happen on the initial load.

To automatically show the first customer detail data add the following to the 
```javascript
$("#CustomerList")
    .listSetData(custList, 
                { 
                    dataTextField: "company", 
                    dataValueField: "id" 
                })
    .triggerHandler("change"); //  force detail to update
```

Note the call to the jQuery `.triggerHandler()` function which forces the change handler to be fired explicitly after the list has been loaded in the code above.

### Saving the customer Data
Now that we have the customer data on the screen, let's update the server with any changes. The way to do this is by capturing the input fields and assigning values to a customer object. 

To do this we need to:

* Capture the last Customer loaded and save it
* Capture the saved input data and store it on the last customer data
* Send the updated customer data to the server

### Capturing the last Customer
In order to save the customer data we'll want to post a complete customer record to the server. But, we're only displaying part of the fields from the server, so rather than have to manually add the fields lets capture the last customer.

We can capture the customer whenever we load a new one by creating a property on the page object and then setting the value after we've loaded a customer.

In the page definition add an `.activeCustomer` property like this:

```javascript
var page = {
    activeCustomer: {},
    ...
}
```
Then in the `.getCustomer()` function store the captured customer to this property:

```javascript
return $.getJSON("customer.csvc?id=" + id)
    .success(function(cust) {                    
        page.activeCustomer = cust;
        ...
    })
```

#### Create the Save Customer Function
Next we need the logic to actually save the customer. To do this we:

* Capture the input data
* Assign it to properties of the activeCustomer
* Post the data to the server

Here's what this looks like in code:

```javascript
saveCustomer: function saveCustomer() {
    var cust = page.activeCustomer;
    cust.id = page.activeCustomer.id;
    if (!cust.id)
        id = 0; // new
        
    cust.lastName = $("#lastName").val();
    cust.firstName = $("#firstName").val();
    cust.company = $("#company").val();
    cust.address = $("#address").val();

    // from ww.jquery.js - posts object as json
   ajaxJson("customer.csvc", cust)
        .success(function (cust) {
            toastr.success("Customer data saved.");
        })
        .error(function (error) {
            toastr.error("Failed to save customer data: " + error.message);
        });
},
```

This code starts by capturing all the input values to the cust object properties. This code is simplified because we're assigning only text. If you're dealing with numbers and dates make sure you appropriate convert the values as needed.

At this point you have a fully configured customer object and we're ready to send it to the server. In this case I use the `ajaxJson` function which is another helper from ww.jquery.js. [ajaxJson](VFPS://Topic/ajaxJson) sends JSON data and retrieves JSON data automatically converting to and from J

```javascript
ajaxJson("customer.csvc", cust)
```

Sends the cust object to the `customer.csvc` on our REST service by converting cust to JSON, and then converting the resulting JSON into an object. The result of the function is the full customer record from the server. Here I just ignore the result and simply display a success message using the toastr pop up.

### Summary
This example is meant to demonstrate how you can use jQuery to capture data from the server for display and editing in your pages.

This type of functionality - capturing small bits of data and updating parts of a page - is a good use case for jQuery, but I wouldn't recommend tackling Single Page Applications using only jQuery alone.  For this a more complete SPA framework is probably a better choice.

That said jQuery is great for managing add-on data for popups or data that needs to be updated once the data has loaded from the server as well as for most basic scripting operations on a page.

In the next step we'll look at using the Angular Framework for building a similar interface to what we built here using a full featured SPA engine.

### Completed Code
For reference here's the full client side code for this application.

#### Html (CustomerDemojQuery.htm)
```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title>jQuery Customer Demo</title>

    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1" />
    <meta name="description" content="" />

    <link rel="shortcut icon" href="./favicon.ico" type="image/x-icon" />
    <meta name="apple-mobile-web-app-capable" content="yes" />
    <meta name="apple-mobile-web-app-status-bar-style" content="black" />
    <link rel="apple-touch-icon" href="touch-icon.png" />

    <link rel="icon" href="./touch-icon.png" />
    <meta name="msapplication-TileImage" content="./touch-icon.png" />

    <link href="./lib/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet" />   
    <link href="./lib/font-awesome/css/font-awesome.min.css" rel="stylesheet" />
    <link href="./lib/toastr/toastr.min.css" rel="stylesheet" />
    <link href="./css/application.css" rel="stylesheet" />
</head>
<body ng-app="app"
      ng-controller="pageController as vm">
    <div class="banner">
        <!-- Slideout Menu Toggle - Hamburger menu -->
        <a class="slide-menu-toggle-open no-slide-menu"
           title="More...">
            <i class="fa fa-bars"></i>
        </a>

        <!-- Icon and Company Logo -->
        <div class="title-bar no-slide-menu">
            <a href="./">
                <img class="title-bar-icon" src="./images/Icon.png" />
                <div style="float: left; margin: 4px 5px; line-height: 1.0">
                    <i style="color: #0092d0; font-size: 0.9em; font-weight: bold;">West Wind</i><br />
                    <i style="color: whitesmoke; font-size: 1.65em; font-weight: bold;">Customer Demo</i>
                </div>
            </a>
        </div>

        <!-- top right nav menu - .menu-hidable for options that hide on small sizes -->
        <nav class="banner-menu-top pull-right">
            <a href="#" class="menu-hidable">
                <i class="fa fa-book"></i>
                Link
            </a>
            <a href="./">
                <i class="fa fa-home"></i>
                Home
            </a>
        </nav>
    </div>


    <div id="MainView">
        <div class="container">

            <div class="page-header-text"><i class="fa fa-list-alt"></i> Customer Demo</div>

            <button id="btnGetCustomerList" class="btn btn-primary">
                <i class="fa fa-download"></i>
                Load Customers
            </button>

            <hr />

            <div class="form-group">
                <label class="control-label" for="form-group-input">Customers</label>
                <select id="CustomerList" class="form-control"></select>

                <hr />

                <div id="CustomerContainer" class="customer-entry" style="display: none">

                </div>
            </div>


            <script id="CustomerTemplate" type="text/template">                
                
                <div class="form-group">
                    <label class="control-label" for="form-group-input">Name</label>
                    <div class="responsive-container">
                        <input id="firstName" type="text" class="form-control" id="form-group-input" placeholder="First Name" value="<#= firstName #>" />
                        <input id="lastName" type="text" class="form-control" id="form-group-input" placeholder="Last Name" value="<#= lastName #>">
                    </div>
                </div>
                <div class="form-group">
                    <label class="control-label" for="form-group-input">Company</label>
                    <input id="company" type="text" class="form-control" id="form-group-input" placeholder="Company"
                           value="<#= company #>" />
                </div>
                <div class="form-group">
                    <label class="control-label" for="form-group-input">Address</label>
                    <textarea id="address" type="text" class="form-control" id="form-group-input" placeholder="Address"><#= address #></textarea>
                </div>

                <div class="margin-top">
                    <button id="btnSaveCustomer" class="btn btn-primary" type="button">
                        <i class="fa fa-check" style="color: lightgreen"></i>
                        Save Customer info
                    </button>
                </div>

            </script>


        </div>  <!-- end #MainView.container -->
    </div> <!-- end #MainView -->


    <footer>
        <a href="http://www.west-wind.com/" class="pull-right">
            <img src="./images/WestwindText.png" />
        </a>
        <small>&copy; Company</small>
    </footer>



    <!-- slide in menu - Remove if you don't use it -->
    <nav class="slide-menu">
        <div style="padding: 10px 10px 10px 3px;">
            <a class="slide-menu-toggle-close">
                <i class="fa fa-bars"></i>
            </a>

            <a class="disabled">
                <i class="fa fa-home"></i>
                Main Menu
            </a>

            <a href="./">
                <i class="fa fa-home"></i>
                Home
            </a>

            <a href="#" class="indented">
                <i class="fa fa-newspaper-o"></i>
                Sub Link
            </a>
            <a href="#" class="indented">
                <i class="fa fa-newspaper-o"></i>
                Sub Link 2
            </a>

            <a href="#">
                <i class="fa fa-rss-square"></i>
                Login
            </a>
        </div>
    </nav>

    <script src="./lib/jquery/dist/jquery.min.js"></script>
    <script src="scripts/ww.jquery.js"></script>
    <script src="./lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
    <script src="CustomerDemojQuery.js"></script>
    <script src="./lib/toastr/toastr.min.js"></script>
    <script>
        $(".slide-menu-toggle-open,.slide-menu-toggle-close," +
          ".slide-menu a, #SamplesLink")
        	.click(function () {
        	    $(".slide-menu").toggleClass("active");
        	});
    </script>
</body>
</html>
```

#### JavaScript (CustomerDemojQuery.js)
```javascript
(function () {
    var page = {
        activeCustomer: {},
        getCustomers: function getCustomerList() {
            // returns an asynchronous promise
            return $.getJSON("customers.csvc")
                .success(function(custList) {
                    page.customers = custList;

                    toastr.success("Customers downloaded.");

                    console.log(custList);

                    $("#CustomerList")
                        .listSetData(custList, { dataTextField: "company", dataValueField: "id" })
                        .triggerHandler("change"); //  force detail to update


                })
                .error(function(parm1, parm2, parm3) {
                    toastr.error("Customer List was not retrieved.");
                });
        },
        getCustomer: function getCustomer() {
            var id = $("#CustomerList").val();
            if (!id) {
                toastr.warning("Please select a customer.");
                return null;
            }

            return $.getJSON("customer.csvc?id=" + id)
                .success(function(cust) {                    
                    page.activeCustomer = cust;
                    toastr.success("Customer loaded:<br/> " + cust.company);

                    var template = $("#CustomerTemplate").html();
                    var html = parseTemplate(template, cust);

                    $("#CustomerContainer").html(html).show();

                })
                .error(function(err) {
                    toastr.error("Customer was not retrieved.");
                });
        },
        saveCustomer: function saveCustomer() {
            var cust = page.activeCustomer;
            cust.id = page.activeCustomer.id;
            if (!cust.id)
                id = 0; // new
            cust.lastName = $("#lastName").val();
            cust.firstName = $("#firstName").val();
            cust.company = $("#company").val();
            cust.address = $("#address").val();

            // from ww.jquery.js - posts object as json
           ajaxJson("customer.csvc", cust, { })
                .success(function () {                   
                    toastr.success("Customer data saved.");
                })
                .error(function (error) {
                    toastr.error("Failed to save customer data: " + error.message);
                });
        },
        initialize: function() {

            // Hook up event handlers
            $("#btnGetCustomerList").click(page.getCustomers);
            $("#CustomerList").change(page.getCustomer);

            // not rendered yet, so we need to use .on() and higher level DOM 
            // node plus a sub-selector to find at runtime.
            $(document).on("click","#btnSaveCustomer",page.saveCustomer);
        }
    };

    page.initialize();
})();
```