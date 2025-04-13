jQueryConfig facilitates consistent script loading for common resources in a page. The reason for this separate component is to allow multiple controls to require and load common resources without loading multiple copies of the same scripts or CSS stylesheets.

### Property Configuration
The primary configuration mechanism are the properties of the jQueryConfig class. 

<ul>
* jQueryCDNUrl
* jQueryFallbackUrl
* jQueryUICDNUrl
* jQUeryUIFallbackUrl
* jQueryUICssBasePath
* jQueryUITheme
* wwJQueryUrl
</ul>

These properties hold default values that are used by default in the Web Control framework and other places. While these values can be overridden by individual controls, typically you'll want to only set these default values once and then not ever set individual configuration values.

### Default Load Values
The various load methods of this class have a lcScriptLocation parameter which can receive one of the following values:
<ul>
* Default - Default location usually CDN if available (for jQuery, jQuery.ui)
* CDN - Load from CDN
* WebResource - Loads from Web Connection ~/scripts folder 
* URL - An absolute URL or a virtual path URL (~/)
</ul>
The best value usually is "Default" which uses the configured values of the jQueryConfig object. This is the most dynamic value that allows consistent loading of scripts across the entire application.


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