Web Connection supports **MVC** (Model, View, Controller) development using built-in HTML scripting that mixes HTML syntax and Foxpro code. Scripts and Templates provide two mechanisms for rendering HTML using two methods - `Response.ExpandScript()` and `Response.ExpandTemplate()` respectively - to render FoxPro expressions and values into a merged HTML template.

MVC makes it relatively painless to create HTML content that mixes both HTML and FoxPro code using a simple approach that uses FoxPro application code you write in a FoxPro class, and HTML with embedded FoxPro expressions that are used to render HTML *Views* from plain text files. By using this approach you get the best of both worlds:

* FoxPro Application Code for Logic Processing
* Easily Editable and Updateable HTML Markup using standard HTML Editors
* Plus: FoxPro Code that can be used inside of the HTML Template for simple render logic

### Web Connection Process Methods are Controllers
The **C**ontroller in MVC is typically the coordinator that ties together the **M**odel - your data or business objects - and then renders a **V**iew of that model.

In MVC the controller retrieves model data from a database or business object, constructs a model (or uses the data directly) and then hands of the model to be rendered by the view. Sounds complicated but it's actually pretty simple:

* A FoxPro Class Method is called with Request Data
* You execute your FoxPro code to produce a result (a cursor, or object(s))
* You Render an HTML View that renders the Result Data as HTML

Web Connection applications use `wwProcess` class methods to implement the controller and HTML templates or scripts for the View. Any model data retrieved can be fed into the script template via `PRIVATE` or `PUBLIC` variables, objects, arrays or collections or  open cursors. You can also call object methods or standalone functions or FoxPro UDFs. Anything that is visible further down the FoxPro call stack can be accessed in scripts and templates.

Here's a simple example of a Process class method (Controller) method that retrieves a Customer object (Model) and then renders it with a script (View):

```foxpro
FUNCTION GetCustomer

lcId = Request.QueryString("id")

LOCAL loCustBus
loCustBus = CREATEOBJECT("cCustomer")
poCustBus.Load(lcId)  

PRIVATE poCust   && Important: Not local so it's visible 
poCust = poCust.oData   && Customer data instance

Response.ExpandScript()
```

`ExpandScript()` accesses the currently active script that was referenced and creates a FoxPro class from it which is then executed. The page is cached until changed. `ExpandTemplate()` is similar but rather than creating FoxPro code it **evaluates** expressions on the page and executes CodeBlocks as independent bits of code.

Generally Scripts are a better choice for applications, Templates work well for pages that only need expressions and no code logic. ExpandTemplate looks like this:

```foxpro
Response.ExpandTemplate()
```

Both `ExpandScript()` and `ExpandTemplate()` can also be passed an explicit script file - rather than the default that matches the method name - to use as the view:

```
Response.ExpandScript("~/customer.wwd")
```

to offload HTML generation to a script or template. You can use a physical path, or use the `~/` to specify the root of the Web site (typically the `/web` folder of the project).

The HTML Scripts/Templates can then access any FoxPro PRIVATE or PUBLIC variables, UDF()s or classes that are in scope and use those to render their output which can be embedded with **expression syntax**:

* `<%= Expression %>`
* `<%: ExpressionHtmlEncoded %>`

If you're running scripts you can also use fully structured commands like:


> #### @icon-warning HTML Encode Text Output
> The second bullet syntax is important: For text rendered from user input you'll want to `HtmlEncode()` the content so that characters like brackets and quotes are fixed up properly. This prevents scripting attacks and avoids formatting errors. In most cases text content should use `<%: %>` rather than `<%= %>`.

Markup typically looks like this for expressions:

```html
<label>Company:</label>
<b><%: poCust.Company %></b>
```

This works well for both scripts and templates. 


If you want to use structured FoxPro Commands like `IF` or `DO` you need to use Scripts with `ExpandScript()`. If you do you can do stuff like this:
 
```html
<h3>Customer List</h3>
<% 
    SELECT * from Customers INTO CURSOR TCustomers
    SCAN
%>

<div class="customer-item">
    <div class="customer-header"><%: TCustomers.Company %></div>
    <div class="customer-body">
        <%: DisplayMemo(TCustomers.Address) %>
    </div>
</div>

<% ENDSCAN %>
```

This script compiles down to FoxPro code that essentially contains the SCAN loop wrapped with literal text embedded inside of the body.

### Partial, Layout Pages and Sections
Templates and scripts also support **Partial Pages**, **Layout Pages** and **Sections** to allow easy reuse of common content. Layout pages allow common content to be stored in a top level 'master' page, into which content pages can load their page specific content. This allows you to keep the common 'page chrome' in a reusable page so you don't have to create - and keep in sync - the same chrome HTML for every page. Content pages then reference the layout page.

Partial pages allow you to create reusable HTML blocks that can be dropped into other pages. Use Partials for reusable components or simply to break large or complex pages into smaller and easier to manage pieces. Partials and Layout Pages both run in the same code context as Content pages meaning the same `PRIVATE` scope variables and data environment state is used and you can use the same expressions and code blocks in Layout and Partial pages. Both of these make it much easier to create consistent and easier to maintain Web sites. I highly recommend review the [Layout Pages, Content Pages, Partials and Sections topic](VFPS://Topic/_4DS19CQWR). It's worthwhile reviewing these concepts carefully, as they help you reduce competitive HTML creation.

### Separate HTML Generation from Controller Logic
The goal of scripts and templates is to separate your controller logic in the method code of your `wwProcess` class and the HTML generation. The controller code is responsible for setting up the variables, data or other state that the View (script or template) then uses to render the HTML result.

The benefit of the external file over hard coding the HTML into the Controller method with say `Response.Write()` is that you can change the markup simply by changing the text. Changing code requires recompilation and redeployment. It's also much easier to edit HTML as text and you can take advantage of powerful HTML editors like Visual Studio, WebStorm, Visual Studio Code or various other editors that support plug-ins for editing HTML.

Templates:

* Allow editing in rich HTML editors
* Allow modification without recompilation
* Allow non-programmers to edit your HTML (designers)

If you are building applications using Web Connection, using scripts or templates is the preferred way to generate HTML output.

### Read More
<%= ChildTopicsList( oHelp, "TOPIC" ) %>