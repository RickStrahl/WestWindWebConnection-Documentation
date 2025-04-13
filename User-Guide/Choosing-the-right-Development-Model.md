Web Connection supports a number of different ways for creating Web applications from very high level approaches like the Web Control Framework to low level approaches where you can use pure code to output every single byte of content sent back over the HTTP connection yourself. Which approach you use depends on your preferences and your application's needs. Here's an overview of some of the approaches available.

* **Low Level Code Only Development**
* **Model View Controller (MVC) Scripts and Templates**
* **REST Services**

### Low Level Code Only Development
Like any Web development framework, Web Connection has **raw HTTP output mechanisms for sending output directly into the HTTP stream**. Web Connection uses these features internally to build core abstractions as well as the higher level frameworks discussed below. The same features are also available to you to use in your application code.

You can use raw `Response.Write()` commands to write out Html from code for example. There are also a number of Html helpers that facilitate generate a few HTML abstractions directly from code. It might be very tempting to build small applications this way. While this works, it generally not a good idea to create Html output purely from code directly in `wwProcess` methods as it ends up creating a lot of extra code that is mixed with your business logic. It's also much harder to edit HTML in code than say in an external HTML editor as you would with MVC for example.

Low level access is useful for many things - if you have requests that return file or image content as part of your application for example, it's useful to create individual methods that specifically use low level `Response.Write()` and header access. If you are building a custom framework on top of Web Connection then too it makes good sense to use the low level access methods. But for typical application code, it's usually not a good choice.

#### Pros
* Logical First Step
* Easy to understand and write

#### Cons
* Difficult to maintain
* Have to recompile for each HTML change
* Can't use sophisticated HTML Editing tools

> **As a general rule:** It's OK to experiment with low level output when you get started, but as you build real applications, it's best to choose another pattern for creating output. Unless you are building a custom framework, or a purely meta data driven output engine type application, code based HTML generation is rarely a good idea.

#### Hello World Example
Raw HTML output is exactly as you would expect: You write out code using the `Response` object directly from within a Process method in your `wwProcess` subclass:

```foxpro
*** wwProcess Method
FUNCTION HelloWord()

lcName = Request.QueryString("name")

TEXT TO lcText NOSHOW TEXTMERGE 
<html>
<body>
   <h1>Hello << lcName >></h1>
   <hr />
   Nice to meet 'ya. Time is: << Time() >>
</body>
</html>

Response.Write(lcHtml)
ENDFUNC
```

### MVC style Scripts and Templates
A very common Web development pattern is known as **MVC**:

* **Model**  
This refers to the data in your applications. This can be in the form of data retrieved from tabels, or view model objects that message data into the required data to be ready for display.

* **View**   
A view is responsible for turning passed in Model data (if any) into output - typically an HTML document. This is done via templates or scripts that typically mix HTML  with code  expressions and bits of control statement code (`if`, `case`, `for`, `while`, `scan` etc.) to produce the final text merged output. In Web Connection this is done via **Scripts and Templates** that let you use FoxPro code within Html templates using ASP like template expansion syntax (`<%= expression %>`).

*  **Controller**  
The controller is the entry point for an MVC request. In Web Connection it's a Process method that receives a request. The controller receives input via the `Request` object, then runs the logic required to create a **Model** from the Request. Controller code should preferrably defer operations to other objects to perform the heavy application logic responsible for retrieving or updating of data using Business objects or other abstracted data operations. The result that is returned can then be sent to the view for rendering. If necessary the result data can be stored into an explicitly created **View Model** object that further formats and messages the data so it's ready for display in the **View**. 

MVC is an attractive strategy for Web development as it results in small bits of Controller code, with the display logic moved into an external HTML template that behaves like a standard HTML document with embedded server logic. Because the file is external, you can use standard HTML editors for editing HTML rather than editing HTML in code. Templates and scripts are **external to your code project**, so you can easily change them without recompiling and redeploying your entire project.

MVC is a commonly used pattern for Web Development so it's familar to developers for other frameworks. If you move to another technology in the future you are likely to find MVC frameworks and might even be able to reuse most of your HTML template content.

[more info on Scripts and Templates](VFPS://Topic/_4DB0VHAQW)

#### Pros
* Very easy to understand and get started with
* Efficient and fast executing code
* Easy to modify templates on the fly
* Use standard HTML editors

#### Cons
* Templates and Scripts have some processing overhead
* Script Compilation at runtime can be confusing

#### Hello World Example
HTML Markup:
```html
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title>HelloWorld</title>
    <link href="../westwind.css" rel="stylesheet" type="text/css" />
</head>
<body>
    <form id="form1" runat="server" method="POST">

    <h1>HelloWorld</h1>
    
    <div class="containercontent">    
        Enter your name:
        <input type="textbox" id="txtName" name="txtName" value="<%= pcName %>" />
        <input type="submit" id="btnSayHello" name="btnSayHello" value="Say Hello" />
        <hr />
        <%= pcMessage %>
    </div>
    
    </form>
</body>
</html>
```

The FoxPro Controller code in a Process class method then looks like this:

```foxpro
FUNCTION HelloWorldTemplate
PRIVATE pcName, pcMessage

*** Variables to embed in the Template View
pcName = ""
pcMessage = ""

*** if we're posting back
IF Request.IsFormVar("btnSayHello")
   pcName = Request.Form("txtName")
   pcMessage = "Hello World " + pcName + ". Time is: " + TIME()
ENDIF

* **Call the template from Web path (HelloWorldTemplate.wwd)
* **Alternately you can explicitly point at a path
Response.ExpandScript( )

*** Some Alternatives
* Response.ExpandScript(Response.GetPhysicalPath())
* Response.ExpandScript("~/SomeOtherPage.wwd")
* Response.ExpandTemplate()

ENDFUNC
```

#### Links:
* <a href="http://www.west-wind.com/webconnection/docs?page=_0nb1al6fm.htm" target="top">Walk-Through with Classic Web Connection Templates</a>


### REST API
You can also build API Services quite easily with Web Connection. APIs are non-visual interfaces that return raw data, rather than HTML. You pass data/parameters to a service, the service does some work based on the input and then returns a data result - most commonly in JSON format. You can then use client side frameworks like Vue, Angular, React etc. to build a front end that consumes these JSON services. You can also build other types of application UIs like native mobile apps, desktop apps, that connect to your REST API and retrieve data from it.

The development approach is very similar to the way you build an MVC application except that instead of passing your model to the Template to render, you simply return it and get it turned into a JSON or XML result. 

In a nutshell every REST endpoint is a method in your Process class, with a single input value and a single result value. Each method can take a single POST or PUT JSON parameter as input, or you can use Form and Query variables just as you can in standard wwProcess handlers and it can return a single value, object or collection which is serialized as JSON.

Input and result objects can be complex - objects, nested objects or collections - that allow you to pass in and return multiple results using a single wrapper. These values can be single FoxPro values (ie. string, number, boolean, date, binary data) or a complex and nested object or a collection or even a FoxPro cursor (which renders as a JSON collection).

REST API services work with a custom `wwRestProcess` class (which is the top level 'handler') that handles the routing of requests to the appropriate methods as well as the  de-serializing incoming JSON data to FoxPro value/objects and serializing the return value into JSON. Again, each endpoint becomes a method in your Process class with a single input and a single result value.


[More info on REST Services](VFPS://Topic/_5az14azpm.htm)

#### What does a REST API Look like?
An API endpoint is just a method inside of a `wwProcess` class with a single input parameter and a return value. The input can map an optional single JSON value, object or collection into a FoxPro object. Your code can then use that object to create new output in the form of a result value, object, collection or cursor.

Here's a simple example:

```foxpro
*********************************************************************
FUNCTION GetData(lvParm)
************************

*** Any posted JSON string is automatically deserialized
*** into lvParm FoxPro object or value 

*** In this example an object with multiple query values
*** was passed
lcSearchName = lvParm.searchName
lcSearchDate = lvParm.searchDate


*** Run your business logic...
select * from customers ;
    where lastName = lcSearchName AND ; 
          entered > lcSearchDate ; 
    into cursor TCustomers
    

*** Simply create objects, collections, values and return them
*** they are automatically serialized to JSON

*** Create an object on the fly here to return 'multiple' values
*** But you can return any object, collection, cursor or value
loObject = CREATEOBJECT("EMPTY")
ADDPROPERTY(loObject,"searchFor",lcSearchName)
ADDPROPERTY(loObject,"serachDate", lcSearchDate)

*** Attach the cursor as a JSON array
ADDPROPERTY(loObject,"customers","cursor:TCustomers")


*** Properties by default serialize to *all lower case*
*** This allows overriding property case for specific properties
Serializer.PropertyNameOverrides = "searchFor,searchDate"

*** Return value of this API - serialized to JSON
RETURN loObject
```

A client could POST to this API with the following JSON:

```json
{
    searchFor: "Strahl",
    searchDate: "2018-09-27T18:00:00Z"
}
```

The method then produces the following output:

```json
{
    searchFor: "Strahl",
    searchDate: "2018-09-27T18:00:00Z",
    customers: [  
      {    
        lastname: "Strahl",
        firstname: "Rick",
        company: "West Wind"
        entered: "2018-09-29T18:00:00Z"
      },
      {    
        lastname: "Doe",
        firstname: "Jane",
        company: "Neverland Inc."
        entered: "2018-09-29T18:00:00Z"
      },
        ...
    ]
}
```

In short it's very simple to create APIs using this interface.

#### Pros
* Very easy to understand and get started with
* Efficient and fast executing code
* Great for any sort of Data access from HTTP clients

#### Cons
* FoxPro JSON conversion issues - casing requires special fix up
* Huge data sets can be slow