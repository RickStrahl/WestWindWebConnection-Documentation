Adding a new customer is actually quite easy. In fact the existing code we have already provides for this functionality for the most part and we only need very minor changes.

To make this work we'll need to:

* Add default behavior for new Customers
* Add a link to the Edit Page without an ID
* Add a Id hidden field to the fields to track the ID

### Override the New() and Save() methods in the Business Object
Typically objects have some sort of default behavior that can be handled when new objects are created. For example, when we create a new customer we probably want to set the `Entered` date to the current date time. Likewise when we save we might automatically want to update the `Updated` field with the current time.

This is easy to do by overriding the default `New()` and `Save()` methods.

```foxpro
************************************************************************
*  New
****************************************
FUNCTION New(llNoNewPk)

DODEFAULT(llNoNewPk)

THIS.oData.Entered = DATETIME()
THIS.oData.Updated = DATETIME()

ENDFUNC
* New


************************************************************************
*  Save
****************************************
FUNCTION Save()

this.oData.Updated = DATETIME()

RETURN DODEFAULT()
ENDFUNC
*   Save
```

With the `New()` method in place we can now get default value to display for the entry page instead of an empty date.

### Add a button to the CustomerList Page
To add a new customer we need to access the `EditCustomer.ctd` page without a parameter, so lets add a new button the customer list page that links to it like this just above the header text:

```html
<a href="editcustomer.ctd"
    class="pull-right btn btn-sm btn-success">
    <i class="fa fa-pencil"></i> New
</a>
```

which looks like this:

![](IMAGES/stepbystep/NewButton.png)

When you click that button you now come up with an empty customer form. Note that the Entered date an bill rate fields are pre-filled.


### New Records in the Controller Method
The good news is the way we wrote the controller method for saving data earlier already handles saving new records. Because we used the following code:

```foxpro
lcId = Request.Params("id")

loCustBus = CREATEOBJECT("cCustomer")
IF !loCustBus.Load(lcId)
   *** if no customer exists lets create a new one
   loCustBus.New()  
ENDIF
```
a new customer object is created when a match can't be found. In that case a new ID is created on the business object when `.New()` is called and when the `.Save()` method is called that new ID is used to create then new customer record. So realistically there are no code changes required! Yay!

### Adding a Hidden ID to the Page
The code as it's written depends on the Query string on the URL to ensure that the edited record stays intact. As extra reliability it's a good idea to store the ID into the form as a field just before the `</form>` tag:

```html
<%= HtmlHidden("Id",poCustomer.Id) %>
```

This ensures that if the URL is somehow changed, the ID is still passed back as part of the page request. `Request.Params("Id")` retrieves either the query string or form variable into the lcId variable.

The business object is smart enough to generate a new ID when it sees an ID that doesn't exist yet, so the rest of the Save logic works without any further code changes.

And this concludes the data access portion of this walk-through.