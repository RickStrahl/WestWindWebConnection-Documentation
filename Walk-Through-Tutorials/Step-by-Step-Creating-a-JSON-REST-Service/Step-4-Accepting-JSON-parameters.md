In the last step we looked at how to render the list of customers, but we didn't really have a way to constrain the list. If we want to filter the list returned we should be able to pass parameters to the request.

There are a couple of ways you can pass 'parameters' to a request

* **Query String Values**  
You can pass query string key value pairs on the URL. This works well for simple value parameters like passing an ID or an option switch, but shouldn't be used for larger amounts of data.

* **A single POST/PUT JSON value or object**  
Your JSON client can send a single value, object or array to your application. If you need to send complex data or multiple single values, wrap them up on the client in a top level object and post them.

### Query String Parameters
To handle query string parameters is the same as in a non-service application by using the `Request.QueryString()` method to retrieve query parameters. 

Let's set this up for the customer list by passing in a single parameter for a company lookup:

```foxpro
************************************************************************
*  Customers
****************************************
FUNCTION Customers()

lcCompany = UPPER(Request.QueryString("Company"))

SELECT id,FirstName,LastName,Company,Entered FROM Customers ;
      ORDER BY Company ;
      WHERE UPPER(Company) = ?lcCompany ;
      INTO CURSOR TCustomers

Serializer.PropertyNameOverrides = "firstName,lastName"

RETURN "cursor:TCustomers"
```
You can now access this from this URL:

<a href="http://localhost/customerdemo/customers.csvc?company=west" target="top">http://localhost/customerdemo/customers.csvc?company=west</a>

which produces an array result of a single matching customer:

```json
[
    {
        id: "_4FG12Y7UD",
        firstname: "Rick",
        lastname: "Strahl",
        company: "West Wind Technologies",
        entered: "2015-11-20T10:00:00Z"
    }
]
```

The key is:

```foxpro
lcCompany = UPPER(Request.QueryString("Company"))
```
which captures the query string value of the `Company=` key passed on the URL. You can add as many query string parameters as you need, but remember it's generally not a good idea to pass lots of data on the URL. Typically the URL is OK to use for IDs, keys and options, and maybe a simple parameter that limits a query as we do here, but don't post 20 values this way. 

### JSON Parameter
REST service methods can receive a single JSON parameter as input. This value is retrieved and deserialized from JSON into a FoxPro value as part of the wwRestProcess processing. You can receive only a single value because the value is retrieved from the POST or PUT buffer of the request and you can only express a single JSON value that way. This doesn't mean that you can only pass a single value however - you can use composition on the client to wrap multiple values or objects into a top level object.

For example, if you wanted to pass firstName, lastName and Company as parameters you can create a JSON wrapper object that holds those values:

```json
{
   firstName: "Rick",
   lastName: "Strahl",
   company: "West Wind"
}
```

which effectively allows you to pass many parameters. You're not limited to simple values either - any properties you add can also be objects of their own.

Let's see how we can apply this by providing filter for firstName, lastName and company using the JSON above. To accept this object in the `Customers` method in our REST Service we can now implement the method like this:


```foxpro
************************************************************************
*  Customers
****************************************
FUNCTION Customers(loFilter)

lcWhere = ""
IF !ISNULLOREMPTY(loFilter)
   IF !ISNULLOREMPTY(loFilter.FirstName)
      lcWhere = "FirstName = loFilter.FirstName AND "
   ENDIF      
   IF !ISNULLOREMPTY(loFilter.LastName)
      lcWhere = lcWhere + "LastName = loFilter.LastName AND "
   ENDIF      
   IF !ISNULLOREMPTY(loFilter.Company)
      lcWhere = lcWhere + "Company = loFilter.Company AND "
   ENDIF      
ENDIF

IF !EMPTY(lcWhere)
	lcWhere = "WHERE " + RTRIM(lcWhere,"AND ","OR ")
ENDIF

SELECT id,FirstName,LastName,Company,Entered FROM Customers ;
      ORDER BY Company ;
      &lcWhere ;
      INTO CURSOR TCustomers

Serializer.PropertyNameOverrides = "firstName,lastName"

RETURN "cursor:TCustomers"
```

Here's what this looks like if you test it with West Wind WebSurge:

![](/images/stepbystep/CustomerListRESTQueryWithWebSurge.png)

The code lets you pass in no filter, or a filter object with any or none of the filter values actually set. The code checks to see if the values are set and if they are they are retrieved. The response then simply returns the results.

> ### @icon-warning Missing Properties
> It's common in JavaScript and JSON to create objects that are missing properties rather than setting values to null. The code above assumes that all the properties on the filter object exist - if they don't the code blows up. If this is a valid assumption then use the code as is. 
>
>Otherwise you need explicitly check for existance of each property using `VARTYPE()`:
> ```foxpro
IF VARTYPE(loFilter.FirstName) = "C" AND !IsNullOrEmpty(loFilter.FirstName)
   ...
ENDIF
```