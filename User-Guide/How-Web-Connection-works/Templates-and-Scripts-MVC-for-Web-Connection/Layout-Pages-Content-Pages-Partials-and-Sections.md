The `Response.ExpandTemplate()` and `Response.ExpandScript()` methods can render **Partial** and **Layout** pages:

* Partial Pages allow nested Page Content to be Loaded into the parent page
* Layout Pages provides a reusable page level container into which content pages can render
* Sections can be added to Layout Pages so Content Pages can inject Markup into Layout Pages

Partial and Layout Pages are tools that can be used to create re-usable HTML and FoxPro script pages that can be used in multiple pages without constantly repeating the same markup.

### Scripts and Templates: Same Syntax
Scripts and Templates use the same basic syntax to describe Layout, Sections and Partials using specific tags that can be embedded into a script or template. But there are differences in [how Scripts and Templates are executed](VFPS://Topic/_05Z0ZM90F):

* Scripts are compiled and run as compiled FoxPro code  
* Templates are evaluated and evaluate expressions at runtime

We recommend that you use Scripts for consistency in most situations, unless you have very simple evaulation only pages.

> ####  Finicky Markup Syntax for Page Directives
> 
> The markup syntax for various page directives are finicky and have to be specified with **exact syntax, double quote characters and spacing**.
> 
> Here's a list of the build-time directives:
> 
> **For Content Pages:**
> 
> - `<% Layout="~/somepage.wcs" %>`
> - `<%= RenderPartial("~/partialPage.wcs") %>`
> - `<%= RenderSection("mysection") %>`
> 
> 
> **For Layout Pages:**
> 
> - `<%= RenderContent("~/somepage.wcs") %>`
> - `<%= section "sectionName" %>`
> - `<% endsection %>`
> 
> 
> These *directives* are parsed out of the document at build or parse time and due to the way they are parsed, the **syntax has to be exact**.


### Content Page
Content pages are the pages that contain your main page content. The top level page you render as a script or template is **always** a content page. Content pages can either be **self contained** which means **it contains a full HTML document**, or it can be an **HTML fragment is merged into a **Layout Page**** that you specify in the header of the content page. All pages called from `ExpandScript()` or `ExpandTemplate() ` are Content pages which are the entry point for any script or template you render.

When you create `CustomerList.wcs` or `CustomerDetail.wcs` the page that is referenced first and starts template/script execution is always the content page.

### Referencing Partial Pages
Content Pages (and also Layout Pages and Sections) can also reference **Partial Pages** which are essentially child pages that are imported into the current page. This is useful for breaking up complex pages into more manageable, smaller **page fragments** that are easier to work with.

Here's a self contained Content Page:


```html
<html>
<head>
   <title>< %= pcTitle %>
</head>
<body>
   Hello < %= pcName %>, the time is <%= TIME() %>.
   <hr>
   <% for x=1 to 10 %>
       <%= "output" + TRANSFORM(x) %>
   <% endfor %>
</body>
</hml>
```

And here's a more complex Content Page that references a Layout page and pulls in content from a Partial page:


```html
<% Layout="~/LayoutPage.wcs" %> 
<% section="headers" %>
     <title>My 3nd Awesome Web Page - <%= Time() %> </title>
     <link href="css/MyPage.css" rel="stylesheet" />
<% endsection %>  
 
<div>           
<h3>This is my CONTENT PAGE</h3>
<p>   
  Time is: <%= DateTime() %> Yup it is!   
</p>
  
<h3>Partial Content below (10x)</h3>  
<hr />  
<% for x = 1 to 5 %>     
    <%= RenderPartial("~/PartialPage.wcs") %>  
<% endfor %>
<hr />
    
           
</div>               
<% section="bottomcontent" %>
    Bottom Content:<br />
    <%= Version() %>
<% endsection %>
<% section="scripts" %>    
    <script src="bower_components/jquery/jquery.js"></script>
<% endsection %>
```

This page contains a Layout section, a Partial reference and a couple of Sections that allow injecting content into a Layout page from the Content Page.

### Layout Pages
A layout page - as the name suggests - provides a **top level layout that can be reused by many pages**. This removes the drudgery of re-creating the boilerplate HTML header, and repeating page layout like menus, sidebars etc. for every page that uses these very same structures. Instead you can create a single Layout page, and then load many Content pages that only render the actual changing content **into the Layout Page**.

Layout Pages always work in combination with Content Pages which load the Layout Page when they are loaded.

> A Content Page essentially asks a Layout Page to render its content into the Layout Page

The **content page is always the top level page** that gets *invoked* by the application, and the `<% Layout %>` directive tells the parser to render the top level page **into the layout page** at the point of `<%= RenderContent() %>`. To make all this work a Content Page must have a `<% Layout %>` directive in the header of the Content Page:

```html
<!-- for example in invoice.wcs -->
<% Layout="~/Views/LayoutPage.wcs" %>
```

Layout pages have an embedded `<%= RenderContent() %>` tag, which is the placeholder where the Content Page content is rendered into the Layout.

Here's a simple layout page:

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>    
    <%= IIF(vartype(pcTitle) = "C",pcTitle,"") %>    
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
    <link href="bower_components/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet" />
    <%= RenderSection("headers") %>
</head>
<body>    
    <%= RenderPartial("~/Views/Common/LoginTag.wcs") %>
   
    <h1>HEADER FROM LAYOUT PAGE</h1>         
    
    Content goes here:
    <%= RenderContent() %>    
    
    <hr />   
    LAYOUT FOOTER 
             
    <%= RenderSection("scripts") %>           
</body>  
</html>
```

##### Content Section
Content gets rendered into the `<%= RenderContent() %>` expression:

```html
<%= RenderContent() %>
```

`RenderContent()` is the most important tag on a Layout Page: It's where the Content Page's content gets embedded. 

The template engine loads the content page, checks for a layout directive and if it finds one, processes the Layout Page first, then renders the output from the Content Page into the `<%= RenderContent() %>` expression resulting in a page that effectively merges the two into a complete HTML document.

##### Sections
If you're new to Layout pages, this may seem like a strange concept but content pages are **contained within a Layout page at specific location** namely where the Layout page calls `<% RenderContent() %>`. A content page cannot direct affect any content in the layout page **except where the result of `<%= RenderContent() %>` is merged in**. If the Content Page needs to add content at the top or bottom of the page, there's no direct way of doing that.

To help with this a Layout page can define **Sections**, which **allow Content pages to inject content into the Layout page** at pre-defined **Sections** that you can create in the Layout page. You can then 'fill the sections' from your Content page's markup.

The default layout page template has two pre-defined sections:

- **headers** - Defined at the bottom of the `<head>` section
Use this to inject additional page headers into the page. For example if you need to add meta tags or custom social media headers. You can also use this to add CSS Stylesheets or scripts that need to load BEFORE the stock script libraries
- **scripts** - Defined at the bottom of the page after script library definitions

These two sections let you perform common tasks of adding CSS and JavaScript to a content page and **let them be rendered** at the appropriate location in the document to work properly. Scripts have to be at the end of the document (generally before the `</body>` tag so they fire after the Document has been defined for example.

**You can add your own sections into a layout page to create additional insertion points.**

To define a section in a `Layout Page` use this syntax by providing a section label:

```html
<%= RenderSection("scripts") %>
```

This creates an *insertion point* in the Layout Page where section text from a Content Page can be rendered.

You can then declare section content in a Content Page using the `<% section %>` directive:


```html
<% section="scripts" %>
<script src="scripts/mypage.js"></script>
<script>
   // your javascript code here
</script>
<% endsection %>
```

This injects the result of the `scripts` section in the Content Page into the `<%= RenderSection("scripts") %>` location of the Layout page.

> ####  @icon-warning Section Content Expressions are Evaluated
> 
> Any expressions defined inside of a `Section` are **always evaluated rather than executed**, even if running inside of a Script page. This means you can't use Codeblocks or structured FoxPro Commands (like `IF` or `DO` except simple `IF` with no `ELSE`) inside of sections nor access LOCAL variables.
> Any variables referenced from scripts must be **PRIVATE** or **PUBLIC** passed down via the FoxPro call stack.
> 
> If you need to do more complex processing, add a `<%= RenderPartial() %>` to the section and add your more complex code to the Partial for normal processing of the content that can run script code. For more info what you can and can't do with with Templates [see here](VFPS://Topic/_05Z0ZM90F).


### Partial Pages

Partial pages are **nested, child pages** that are rendered from a content or layout page.

To reference a partial page embed the following:


```html
<%= RenderPartial("~/Views/Common/LoginTag.wcs") %>
```

`RenderPartial()` embeds the content of another page into the current Content Page or Layout Page. You pass a Web site relative path starting with the `~` which signifies the site root path, followed by the path to the page you want to pull into the current page. The partial page is then executed as a script or template and the content injected into the page replacing the `<%= RenderPartial() %>` expression.

We've already seen partial pages called in the other examples. Partial pages are typically HTML fragments, not complete pages. A good example of a partial page might be the Login/Account Status section at the top of the page. You can create the partial once and re-use it in multiple pages.


```html
<div class="login-widget">
    <% if poUser.IsAuthenticated %>
       <a href="profile.wcs"><%= poUser.Username %></a>
    <% else %>
       <a href="login.wcs">Login</a>
    <% endif %>
</div>
```

To use the partial you then simply reference the page where the script is stored from another script.


```html
<%= RenderPartial("~/LoginWidget.wcs") %>
```

Partials are great for breaking up complex pages into smaller more manageable sub-pages as well as providing re-usability. For example, if you have a complex list display you might want to break the individual rows or even individual columns out to be rendered from a partial to make the HTML more readable.

### Putting it all together

Building applications that use one or more Layout pages, with a number of Sections, and then using Content pages to create the actual content for pages in small self contained blocks, and using Partial Pages to breakdown complex pages, or create small reusable page fragments/widgets (like a Login Widget for example) make HTML creation much more manageable.  

You don't have to copy and paste large blocks of common HTML into every page or render partial scripts into each page. Rather you let the Layout page handle the 'top level' layout of the page, with a few holes left to fill for the main `RenderContent()` and with `Section` directives to allow any content pages to fill in other common areas of the page.

Partials are useful to create reusable page components, or to break up large complex pages into smaller more manageable chunks.

Using all of these in combination makes it much easier to create complex applications and it's an effective workflow that minimizes repeated markup.