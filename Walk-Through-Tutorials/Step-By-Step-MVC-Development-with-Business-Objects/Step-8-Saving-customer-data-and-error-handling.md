Now you're ready to save the data when you click the **Save** button. In this step we want to handle the following:

* Capture the POSTed form data
* Bind the form data back to our business object
* Validate the data
* Display any error information
* Save the data to the database


### Capturing raw POST data
In its simplest form you can use `Request.Form()`, `Request.FormDate()`, `Request.FormChecked()` and `Request.FormSelected()` to capture raw input variables:

```foxpro
IF Request.IsPostback()
    poCustomer.oData.Company = Request.Form("Company")
    poCustomer.oData.Entered = Request.FormDate("Entered")
    poCustomer.oData.IsActive = Request.FormChecked("Active")
    ...
ENDIF
```

While this works there are a few problems with this. First, if there is some sort of binding error and the value cannot be found or converted you don't get notified. And secondly - this process is tedious if you're dealing with a large form and has to be maintained everytime a new field is added.

There's a better way.

### Using Request.UnbindFormVars()

So rather you can use bulk unbinding using the [Request.UnbindFormVars()](VFPS://Topic/_4FH15YHRR) which unbinds form variables to an object and provides you with error information. We can then use that error information to display errors on the individual controls as well as an error summary.

```foxpro
FUNCTION EditCustomer()

*** Querystring and Form Vars
lcId = Request.Params("id")

loCustBus = CREATEOBJECT("cCustomer")
IF !loCustBus.Load(lcId)
   *** if no customer exists lets create a new one
   loCustBus.New()  
ENDIF

PRIVATE poErrors, poCustomer

*** Error display handler for binding errors and messages
poError = CREATEOBJECT("HtmlErrorDisplayConfig")
poCustomer = loCustBus.oData

IF (Request.IsPostBack())
   poError.Errors = Request.UnbindFormVars(loCustBus.oData)

   IF !loCustBus.Validate()
       poError.Errors.AddErrors( loCustBus.oValidationErrors )
   ENDIF
   
   IF poError.Errors.Count < 1 
       IF !loCustBus.Save() 
	      poError.Message = loCustBus.cErrorMsg      
       ENDIF  
   ENDIF

   IF (poError.Errors.Count > 0)
       poError.Message = poError.Errors.ToHtml()
   	   poError.Header = "Please fix the following form entry errors"
   ELSE
       poError.Message = "Customer saved."
       poError.Icon = "info"
       *** display message then reload list
       Response.AppendHeader("Refresh","2;url=customerlist.ctd")
   ENDIF               
ENDIF
 

Response.ExpandScript()
ENDFUNC
```


The interesting stuff in this code happens in between the `IF Request.IsPostback()` block. The code starts by unbinding the form variables into the loCustBus.oData object (same as poCustomer instance) using `Request.UnbindFormVars()`. This function matches form variables with the same name to object properties and return a collection of binding errors which can occur if you unbind values and there's a type conflict (trying to unbind characters into a number or an invalid date). UnbindFormVars unbinds only simple values, and child objects are not unbound. For child objects you can use additional UnbindFormVars() calls using object prefixes in the varnames (ie. Customer.Address.Street).

### Business Object Validation
Next the code tries to validate the updated values on `.oData` member the business object by calling the `.Validate()` method. By default this method is empty and no validation occurs.

But... you can override the `.Validate()` method to create validation rules so it's easy to verify whether the object can be saved to disk. Here are a few simple ruless to apply in our `cCustomer` class:

```foxpro
************************************************************************
* cCustomer ::  Validate
****************************************
FUNCTION Validate()

DODEFAULT()

*** Always clear errors first in case you're reusing
this.oValidationErrors.Clear()

IF EMPTY(this.oData.Company)
   this.AddValidationError("Company can't be left empty.","Company")
ENDIF
IF EMPTY(this.oData.LastName)
   this.AddValidationError("Lastname must be provided.","LastName")
ENDIF
IF EMPTY(this.oData.FirstName)
   this.AddValidationError("Firstname must be provided.","FirstName")
ENDIF


*** Date over 20 years old not allowed
IF this.oData.Entered < DATE() - 20 * 365
	this.AddValidationError("Entered date can't be longer than 20 years ago","Entered")
ENDIF

IF THIS.oValidationErrors.Count > 0
	this.SetError( this.oValidationErrors.ToString() )
	RETURN .F.
ENDIF

RETURN .T.
```

You run any code to validate the values of the object. This can be any code and it could make queries to the database to validate more complex rules. You essentially use `.AddValidationErrors()` to add errors. You provide an error message and a field name that it applies to. The field name can then be mapped to a control on the page.

Once this is inplace you can then validate the business object simply with:

```FoxPro
IF !loCustBus.Validate()
   poError.AddErrors( loCustBus.oValidationErrors )
ENDIF
```

This assigns the Validation Errors to the `poError.Errors` collection which is a collection of [wwValidationError](VFPS://Topic/_4FH1DJRYR) objects. The two collections are simply combined in this call.

If the error count for the combined collection is greater than 0 there are errors and we can't save the Customer, but rather display an error message, so the code sets the `poError.Message` and `.Header` properties to cause an error message to be displayed.

The form then displays the binding errors as a summary as well as associated with the individual controls that have an error:

![](IMAGES/stepbystep/CustomerEdit_BindingErrors.png)

You can then fix the errors and resubmit the form to save the customers.

When all the information is valid, this simple code saves the customer information to the database:

```foxpro
IF poError.Errors.Count < 1 
   IF !loCustBus.Save() 
      poError.Message = loCustBus.cErrorMsg      
   ENDIF  
ENDIF
```

In good keeping with separation of concerns your business logic related to saving data should be wrapped up in the business logic so that you don't have to handle the data access in this code. The `.Save()` method by default saves the current oData member to to the database via `GATHER NAME` in FoxPro, which in this case is all we need. In more complex scenarios you probably will override the `.Save()` method to add additional logic for saving child records or other related information.

### Handling Confirmation and Error Display
Once we've either saved or determined there's an error we have to notify the user so the last block of code in the postback block deals with that:

```foxpro
IF (poError.Errors.Count > 0)
   poError.Message = poError.Errors.ToHtml()
   poError.Header = "Please fix the following form entry errors"
ELSE
   poError.Message = "Customer saved."
   poError.Icon = "info"
   *** display message then reload list
   Response.AppendHeader("Refresh","2;url=customerlist.ctd")
ENDIF               
```

This code sets properties on the poError object depending on success or failure. On failure we set the Message detail to a list of errors - both binding errors and validation errors serialized into an HTML list - as well as adding a header. That gives the display you see in the last screen shot with the error box on top.

This is then displayed with the simple:

```html
<%= HtmlErrorDisplay(poError) %>
```

which takes the Message, the Header and Icon properties to render the alert box at the top of the page. Each of the binding or validation errors is displayed via the `< %= HtmlBindingError()  % >` expressions in the page after each field. 

```html
<div class="col-sm-7">
    <%= HtmlTextBox("FirstName",poCustomer.FirstName,
                    [class="form-control" placeholder="Enter the first name"]) %>
    <%= HtmlBindingError("FirstName",poError.Errors) %>
</div>
```

### Success Message and Redirect
When there are no errors and the customer info has been saved, we still want to display a quick note that the save operation worked, and then navigate back to the customer page. To do this I first display the page with the success message by setting the `.Message` and `.Icon` properties of the `poError` object to display the success. Then I use an HTTP header to force the page to automatically refresh after a couple of seconds:

```foxpro
poError.Message = "Customer saved."
poError.Icon = "info"

*** reload list after 2 seconds
Response.AppendHeader("Refresh","2;url=customerlist.ctd")
```

And voila, we've saved our data.