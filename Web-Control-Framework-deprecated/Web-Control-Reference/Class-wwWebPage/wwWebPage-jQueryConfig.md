Object that simplifies loading of jQuery, jQuery UI and ww.jquery.js programmatically into the page.

Loads:
<ul>
* jQuery
* ww.jquery
* jQueryUI
</ul>

This object provides the ability to load libraries efficiently and load them only once per page as well as using a default location to load the library from. The object also allows loading libraries direct as string values (<script> references) that can be embedded into a page using <%= %> tags.

Methods include:

<ul>
* IncludejQuery(lcScriptLocation,llAsString)
* IncludewwjQuery(lcScriptLocation,llAsString)
* IncludejQueryUi(lcScriptLocation,llAsString)
</ul>

These functions return strings of full <script> tags (jQuery example):

```html
<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.6.3/jquery.js" type="text/javascript" ></script>
<script type="text/javascript">
if (typeof(jQuery) == 'undefined')
   document.write(unescape("%3Cscript src='/wconnect/scripts/jquery.js' type='text/javascript'%3E%3C/script%3E"));
</script>
```

Note that jQuery (and jquery.ui) add local fallbacks in case CDN urls can't be reached.

The lcScriptLocation parameter can be specified as:

<ul>
* Default         - Default location usually CDN if available (for jQuery, jQuery.ui)
* CDN             - Load from CDN
* WebResource - Loads from Web Conneciton ~/scripts folder
* Url                - Specify an explicit url (used for internal operation)
</ul>

### Configuring jQueryConfig Script Locations and Options Globally
The jQueryConfig class works via a PUBLIC _JQUERY_CONFIG variable that is loaded and stored in memory. You can customize this variable at any time during your application's startup to configure where scripts are loaded from. For example you might do the following in your server initialization:

```foxpro
FUNCTION OnLoad()

* ... other onload code

loConfig = LoadjQueryConfig()  && Gets public instance
loConfig.jQueryCDNUrl =  "https://ajax.googleapis.com/ajax/libs/jquery/1.6.2/jquery.min.js"
loConfig.jQueryUiCDNUrl = "https://ajax.googleapis.com/ajax/libs/jqueryui/1.8.16/jquery-ui.min.js"
jQueryFallbackUrl = "~/myscripts/jquery.min.js"
jQueryUIFallbackUrl = "~/myscripts/jquery-ui.min.js"

ENDFUNC
```

Once configured in this way, any use of the Page.jQueryConfig properties and methods will use these modified values.

### Using the Class from Non WebPage Content in Classic Web Connection
Although the jQueryConfig class is primarily meant for script embedding into Web Control Pages it can also be used in classic environments. Using the class facilitates loading jQuery consistently and easily embedding of a complex jQuery CDN url into pages dynamically.

To use the class in a standard page (say with Templates or Scripts) you:

<ul>
* Retrieve an instance of the jQueryConfig your code
* Embed the script references into a page using one of the IncludeXXX() methods
* Pass the llAsString parameter to retrieve the script references as a string
</ul>

To load the instance you use the global LoadjQueryConfig() function that loads the PUBLIC instance:

```foxpro
poConfig = LoadjQueryConfig()  && Public instance
```

Then to embed values into the page:

```html
<html>
<head>
   <title>blah</title>
     <%= poConfig.IncludejQuery("Default",.T.) %>
     <%= poConfig.IncludejQueryUi("Default",.T.) %>
     <%= poConfig.IncludewwjQuery("Default",.T.) %>
</head>
<body>
</body>
</html>
```

Alternately if you use code you can just write out the script reference directly using Response.Write():

```foxpro
loConfig = LoadjQueryConfig()  
Response.Write(loConfig.IncludejQuery("Default",.T.))
Response.Write(loConfig.IncludejQueryUi("Default",.T.))
Response.Write(loConfig.IncludewwjQuery("Default",.T.))
```