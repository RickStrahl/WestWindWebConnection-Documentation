This topic describes how to deal with complex types in Web Services. I'll use a .NET Web Service on the server as an example here because it's a common scenario, but it should work with any WebService that is capable of working with complex typed parameters or return value.

For this example, I'll use a complex object that is based on the .NET samples available from the 
<a href="http://www.west-wind.com/presentations/foxpronetwebservices/foxpronetwebservices.asp" target="top">Using Visual FoxPro to call  .NET Web Services for Data Access</a>. This article covers things in a generic way. Here I'll talk about using wwSOAP more extensively which provides more flexibility.

### Retrieving a Complex object from the Web Server
Let's assume we have a complex object defined on the Web Server that is an Author object based on the Pubs database. For making things a little more interesting I'll add a child object - PhoneNumbers - and a child array Invoices to this Author object.

This ficticious object then looks something like this (for more details on the SimpleEntity class see the article above):

```cs
[Serializable()]
public class AuthorEntity : SimpleEntity
{			
	protected DataRow Row;

	public PhoneInfo PhoneNumbers = new PhoneInfo();

	public String Au_id;
	public String Au_lname;
	public String Au_fname;
	public String Phone;
	public String Address;
	public String City;
	public String State;
	public String Zip;
	public Boolean Contract;
	
	public Invoice[] Invoices = null;

	public AuthorEntity()
	{
		ArrayList al = new ArrayList();
	
		Invoice inv = new Invoice();
		inv.InvoiceNumber = "1";
		al.Add( inv );
		
		inv = new Invoice();
		inv.InvoiceNumber = "2";
		inv.InvoiceTotal = 200.00M;
		al.Add(inv);
		
		this.Invoices = al.ToArray(typeof( Invoice ) ) as Invoice[];
	}
}

[Serializable()]
public class PhoneInfo 
{
	public string HomePhone = "808 579-8342";
	public string WorkPhone = "808 579-8342";
	public string Fax = "808 801-1231";
	public DateTime ServiceStarted = Convert.ToDateTime("01/01/1900");
}

[Serializable]
[SoapInclude( typeof(Invoice)) ]
public class Invoice 
{
	public string InvoiceNumber = "";
	public decimal InvoiceTotal = 100.00M;
	public DateTime InvoiceDate = DateTime.Now;

	public void NewInvoiceNumber() 
	{
		this.InvoiceNumber = new Guid().GetHashCode().ToString("x");	
	}
}
```

On the server side we can then create a Web Method like this to retrieve an object instance from this definition:

```cs
[WebMethod(Description = "Returns an individual Author by ID as an Entity Object.")]
public AuthorEntity GetAuthorEntity(string Id) 
{
	busAuthor Author = new busAuthor();

	if (!Author.Load(Id))
		return null;

	AuthorEntity Auth = new AuthorEntity();
	Auth.LoadRow(Author.DataRow);

	return Auth;
}
```

This is overly simplified, but it does createa an object that includes the child object (with default data) and a child array (also with the default data dumped in on the constructor). This generates a serialized object graph that looks like this:

```xml
<AuthorEntity>
	<PhoneNumbers>
		<HomePhone/>
		<WorkPhone/>
		<Fax/>
		<ServiceStarted>2004-10-25T13:35:50</ServiceStarted>
	</PhoneNumbers>
	<Au_id/>
	<Au_lname>Strahl</Au_lname>
	<Au_fname>Rick</Au_fname>
	<Phone>808 123-1211</Phone>
	<Address>32 Kaiea Place</Address>
	<City>Paia</City>
	<State>HI</State>
	<Zip>96779</Zip>
	<Contract>0</Contract>
	<Invoices>
			<Invoice>
				<InvoiceNumber>9</InvoiceNumber>
				<InvoiceTotal>102</InvoiceTotal>
				<InvoiceDate>2004-05-10T00:00:00</InvoiceDate>
			</Invoice>
			<Invoice>
				<InvoiceNumber>10</InvoiceNumber>
				<InvoiceTotal>199.90</InvoiceTotal>
				<InvoiceDate>2004-01-10T00:00:00</InvoiceDate>
			</Invoice>
	</Invoices>
</AuthorEntity>
```

To receive this object via SOAP from the Web Server we can then use code like the following:

```foxpro
oSOAP = CREATEOBJECT("wwSOAP")
oSOAP.nHttpConnectTimeout = 5
oSOAP.lParseReturnedObjects = .T.   && Important: Makes sure object gets parsed

* ** Retrieve authors - returns DataSet returned as NodeList
oSOAP.AddParameter("Id","486-29-1786")

loAuthor = oSOAP.CallWSDLMethod("GetAuthorEntity",lcWSDL) 

* ** Main Author
? loauthor.au_lname

* ** Child Object
? loAuthor.PhoneNumbers.HomePhone
? loAuthor.PhoneNUmbers.ServiceStarted

* ** Invoices Array
? loAuthor.Invoices[1].InvoiceNumber
? loAuthor.Invoices[1].InvoiceDate
? loAuthor.Invoices[2].InvoiceNumber
? loAuthor.Invoices[2].InvoiceDate

RETURN
```

In this case wwSOAP takes care of all the parsing yourself by creating the object on the fly and then sticking the property values into it. The object that wwSOAP creates is basically a proxy object - a mere data container that contains only properties and no logic.

If you already have a matching object structure in place you can also pass in an existing object, which might work better if you have a real business object with methods. That way you don't have to copy data between the proxy and whatever real data structures you're building. 

To demonstrate I'll create an object in code on the fly, but assume for a second that this object is your own business object:

```foxpro
FUNCTION GetAuthorProxyObject()

* ** Manunally parse the result back into an object
loAuthor = CREATEOBJECT("Relation")
loAuthor.AddProperty("Au_id","")
loAuthor.AddProperty("Au_lname","")
loAuthor.AddProperty("Au_fname","")
loAuthor.AddProperty("Phone","")
loAuthor.AddProperty("Address","")
loAuthor.AddProperty("City","")
loAuthor.AddProperty("State","")
loAuthor.AddProperty("Zip","")
loAuthor.AddProperty("Contract",.f.)

loAuthor.AddProperty("PhoneNumbers",CREATEOBJECT("RELATION"))
loAuthor.PhoneNumbers.AddProperty("HomePhone","")
loAuthor.PhoneNumbers.AddProperty("WorkPhone","")
loAuthor.PhoneNumbers.AddProperty("Fax","")
loAuthor.PhoneNumbers.AddProperty("ServiceStarted",DATETIME())


* ** Code to add an array for invoices
loAuthor.AddProperty("Invoices")
* DIMENSION loAuthor.Invoices[2]


* LOCAL loInvoices as Collection
loAuthor.Invoices = CREATEOBJECT("Collection")

loInv = CREATEOBJECT("Empty")
ADDPROPERTY(loInv,"InvoiceNumber","9")
ADDPROPERTY(loInv,"InvoiceDate",{05/10/2004 :})
ADDPROPERTY(loInv,"InvoiceTotal",102.00)

loAuthor.Invoices.Add(loInv)
* loAuthor.Invoices[1] = loInv

loInv = CREATEOBJECT("Empty")
ADDPROPERTY(loInv,"InvoiceNumber","10")
ADDPROPERTY(loInv,"InvoiceDate",{01/10/2004 :})
ADDPROPERTY(loInv,"InvoiceTotal",199.90)

loAuthor.Invoices.Add(loInv)

RETURN loAuthor
```

Now I can pass this object to oSOAP.CallWSDLMethod.

```foxpro
oSOAP = CREATEOBJECT("wwSOAP")
oSOAP.nHttpConnectTimeout = 5
* oSOAP.lParseReturnedObjects = .F.  && Not required .F. is default

* ** Get the object to parse into
loAuthor = GetAuthorProxyObject()

loAuthor = oSOAP.CallWSDLMethod("GetAuthorEntity",lcWSDL,loAuthor)
```

And voila there you have a couple of ways to send complex object across the wire.

### Sending a Complex Object to the Server
So now let's look at the reverse process of sending an object to the Web Service. the following work with the same data structures as above. Here's an UpdateAuthorEntity method in the Web Service:

```cs
[WebMethod(Description = "Updates or adds an individual Author from a passed in AuthorEntity object.")]
public bool UpdateAuthorEntity(AuthorEntity UpdatedAuthor) 
{
	if (UpdatedAuthor == null)
		return false;

	busAuthor Author = new busAuthor();

	if (!Author.Load(UpdatedAuthor.Au_id))  
	{
		if (!Author.New()) 
			return false;
	}

	UpdatedAuthor.SaveRow(Author.DataRow);

	if (!Author.Save())
		return false;

	return true;
}
```

As before this uses the AuthorEntity which will serialize into the same XML shown above.

Notice that the property names are proper case, which is something that's difficult to accomplish with Visual FoxPro natively since you cannot create properly cased property names that can be read by VFP's internal commands. Normally to generate XML like this you could use wwXML::ObjectToXML() but because of the case sensitivity this doesn't work correctly unless the Web Service properties are all lower case.

wwSoap helps here with a couple of methods that provide Creating and object from the WSDL schema and taking a FoxPro object and creating XML by mapping properties to the schema to provide properly cased XML. The two methods are [CreateObjectFromSchema()](vfps://Topic/_2C30SQQIW) and [CreateObjectXmlFromSchema()](vfps://Topic/_1CH14O1AJ) respectively. 

CreateObjectFromSchema basically allows creation of a FoxPro object from the WSDL schema. It creates an instance with the properties of the service. Child objects are automatically created for you hierarchically and each simple property is assigned its default value. Arrays/Collections are mapped to FoxPro Collections which are created empty (ie. CreateObject("Collection") ) - you're responsible for creating the required child objects. You can use arrays or Collections to do this, but Collections make this process a little easier. 

```foxpro
LOCAL loSoap as wwSOAP
loSoap = CREATEOBJECT('wwSoap')
loSoap.Parseservicewsdl(lcWSDL,.T.)  && Force parsing of objects

* ** Create a FoxPro object from the WSDL Schema definition
loAuthor = loSoap.CreateObjectFromSchema("AuthorEntity")

* ** Assign values to the object instance - remember this is a proxy
loAuthor.au_lname = "Strahl"
loAuthor.au_fname = "Rick"
loAuthor.Phone = "808 123-1211"
loAuthor.Address = "32 Kaiea Place"
loAuthor.City = "Paia"
loAuthor.State = "HI"
loAUthor.Zip = "96779"
loAuthor.Contract = 0

loInvoice = loSoap.CreateObjectFromSchema("InvoiceEntity")
loInvoice.OrderDate = DateTime()
loInvoice.OrderNumber = SYS(2015)

* ** Add the invoice to the Invoices collection
loAuthor.Invoices.Add(loInvoice)

* ** Simply add the Author parameter  and specify WSDL Type
loSOAP.AddParameter("UpdatedAuthor", loAuthor, "AuthorEntity")

* ** Call the actual Web ServiceMethod
llResult = loSOAP.CallWsdlMethod("UpdateAuthorEntity",lcWSDL)

? loSOAP.cErrorMsg
? loSoap.cRequestXml  && See what was sent
? llResult

RETURN
```

This gives you high level support for calling with a service with complex parameters. 

Note however that there may be problems with this approach. Certain values may not be understood correctly by the WSDL. For example, Enumerated values often are not picked or specialty types can be incorrect. 

### Manual XML Parameters Submission
The automated approach is easiest if it works. If you're working with wwSOAP or .NET services chances are that the above approach will work without problems. However there might be situations where this approach is not sufficient and you may have to explicitly format the parameters or the entire SOAP body to send to the server.

[wwSOAP::AddParameter](vfps://Topic/_06F04LMKH) supports various flags for the parameter formatting including XML, RAWXML and NODELIST. XML allows you to create the body of a parameter without its root node. IOW, you provide the XML and the method wraps the parameter name around it. RAWXML does the same except it doesn't wrap the parameter name around it. Typically RAWXML is what you want if you're sending a parameter in raw form. 

To see what the XML output is look at the .cRequestXml property to determine whether the output matches what you are expecting. 

For example, to send a customer object parameter explicitly created:

```foxpro
TEXT TO lcParm NOSHOW
<Customer>
	<Name>Rick Strahl</Name>
	<Company>West Wind</Company>
	<Address>
		<Street>32 Kaiea</Street>
		<Zip>96679</Zip>
	</Address>
</Customer>
ENDTEXT
loSoap.AddParameter("AddCustomer",lcParm,"RAWXML")
```

This assumes the parameter name is Customer and replaces the entire parameter definition in the SOAP body.