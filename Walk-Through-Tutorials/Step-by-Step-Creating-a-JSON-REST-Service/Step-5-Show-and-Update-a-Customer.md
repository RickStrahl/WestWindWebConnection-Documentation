At this point you've already seen how to do all the things you typically need to do with a REST Service:

* Display data results
* Capture query string input values
* Capture a single JSON input parameter


Using these three core features, lets round out our customer CRUD operation REST API by implementing display of a single customer and updating a customer.

### Displaying a Customer Record
In order to display a customer record we'll need a single ID parameter passed that gives us access to the primary key to then load the customer.

To do this using the business object we can use this simple code:

```foxpro
*** At the class level
cCustomerCapitalizations = "firstName,lastName,careOf"

************************************************************************
*  Customer
****************************************
FUNCTION Customer(loParm)

lcId = Request.QueryString("id")  && string pk

IF EMPTY(lcId)
   ERROR "Invalid customer id"
ENDIF

loCustBus = CREATEOBJECT("cCustomer")

IF !loCustBus.Load(lcId)
    ERROR loCustBus.cErrorMsg
ENDIF 

Serializer.PropertyNameOverrides = this.cCustomerCapitalizations

*** return .oData member that contains customer **object**
RETURN loCustBus.oData
```

The key here again is the `Request.QueryString("id")` method call that retrieves a query string parameter for this request:

<a href="http://localhost/customerdemo/customer.csvc?id=_4FG12Y7UD" target="top">http://localhost/customerdemo/customer.csvc?id=_4FG12Y7UD</a>

which results in this single customer **object** being turned into a JSON response:

```json
{
  "address": "33 Kaiea Place\r\nPaia, HI 96779",
  "billRate": 120,
  "btype": "LABOR_PRG",
  "careOf": "Rick Strahl",
  "company": "West Wind Technologies",
  "email": "rstrahl@west-wind.com",
  "entered": "2015-11-20T10:00:00Z",
  "firstName": "Rick",
  "id": "_4FG12Y7UD",
  "inactive": true,
  "lastName": "Strahl",
  "phone": "(503) 914-1111",
  "state": "",
  "updated": "2016-01-11T23:50:37Z"
}
```
The result is an individual JSON object with properties for each of the customer records properties.

> #### @icon-warning Single Record Cursors do not return an Object!
> In the code above using the business object the code retrieves a single record from the database via a `SELECT` and then implicitly turns the first record into an object on the `.oData` member via the `Load()` method <small>*(as part of wwBusiness)*</small>.
>
> If you intend to return an object result and the result comes from Cursor or Table, the result is still **always an array - not an object** even if you are returning a single record!
>
>In order to return an object, you have to select a record from the cursor and use `SCATTER NAME` to turn it into an object:
>
>  ```foxpro
>  SELECT * FROM customers WHERE id = "_123"  
> SCATTER NAME loCustomer MEMO  && first record - otherwise use GO or SKIP
>
>  *** Return customer record as JSON
>  RETURN loCustomer
>  ```

### Updating a Customer Record
To update a customer record we essentially pass in a customer record that has the updated values we need to assign to an existing customer object. 

The sequence is:

* Capture the updated JSON data
* Load the customer record that is to be updated
* Update the individual values
* Validate and save the updated values 

Here's the code that handles an update:

```foxpro
************************************************************************
*  SaveCustomer
****************************************
FUNCTION SaveCustomer(loCust)

loCustBus = CREATEOBJECT("cCustomer")

IF !loCustBus.Load(loCust.id)
   *** creates empty oData with ID set
   loCustBus.New()
ENDIF

loData = loCustBus.oData

loData.Company = loCust.Company
loData.FirstName = loCust.FirstName
loData.LastName = loCust.LastName
loData.Address = loCust.Address
loData.Email = loCust.Email
loData.Phone = loCust.Phone

*** You can also do a wholesale update with:
* CopyObjectProperties(loCust,loCustBus.oData,1)

IF !loCustBus.Validate()
   ERROR loCustBus.oValidationErrors.ToString()
ENDIF

IF !loCustBus.Save()
   ERROR loCustBus.cErrorMsg
ENDIF

RETURN loCustBus.oData   
ENDFUNC
```

The `loCust` parameter holds the updated customer data sent from the client via JSON. From that we can read the ID and load up the existing customer record and the associated customer ID. We read that and call `.Load()` on the business object, and then update the `oData` member with the data sent from the client. 

This code uses manual assignments, but you can also choose to use `CopyObjectProperties()` which allows copying of all properties in bulk depending on whether you need more control over the update process.

Finally you can validate and then save the object back to disk. 

This code uses a wwBusiness object, but you can employ a similar approach using raw database values (using REPLACE in a selected record) or simply creating and `UPDATE` statement and updating the data in the database. What you do in this method to handle the save logic is up to your business use case.

The end result here is that we return the updated record to the client.

### Reuse the Customers URL for Display and Save Operations
REST applications often reuse the exact same URL for different HTTP Verb operations. Right now we have `Customer.csvc` request and a `SaveCustomer.csvc` request, but in terms of REST this is considered bad form as you are supposed to have URLs that represent nouns whenever possible. Per that concept there should be a `Customer.csvc` endpoint that handles display, update and creation requests via `Customer.csvc`.

FoxPro doesn't allow for method overloading so we can have only one `Customer` method, but we can inspect the HTTP headers and determine which Verb was used for the request and then route to another method.

So, let's 'overload' the Customer method as follows:

```foxpro
************************************************************************
*  Customer
****************************************
FUNCTION Customer(loParm)

lcVerb = Request.GetHttpVerb()

*** PUT and POST save
IF INLIST(lcVerb,"PUT","POST")
   RETURN THIS.SaveCustomer(loParm)
ENDIF   
IF lcVerb = "DELETE"
   RETURN THIS.DeleteCustomer(loParm)
ENDIF

*** HTTP GET shows customer

lcId = Request.QueryString("id")  && string Pk
IF EMPTY(lcId)
   ERROR "Invalid customer id"
ENDIF

loCustBus = CREATEOBJECT("cCustomer")

IF !loCustBus.Load(lcId)
    ERROR loCustBus.cErrorMsg
ENDIF 

Serializer.PropertyNameOverrides = this.cCustomerCapitalizations

*** return oData member that contains customer object
RETURN loCustBus.oData
```

### Deleting a Customer
For completeness sake for our CRUD operations let's add the DELETE operation. We can delete using the HTTP DELETE verb and by adding a `DeleteCustomer()` method to the class:

```foxpro
************************************************************************
*  DeleteCustomer
****************************************
FUNCTION DeleteCustomer()

lcId = Request.QueryString("id")

loCustBus = CREATEOBJECT("cCustomer")
IF !loCustBus.Delete(lcId)
   ERROR loCustBus.cErrorMsg
ENDIF

RETURN .T.
ENDFUNC
```

This method is very simple in that it simply retrieves the ID from the URL query string and then tries to delete the customer using the business object. Simple.

I'm omitting one pretty critcal piece in this delete operation and also the update operation: Security. We'll come back to this later in this tutorial.