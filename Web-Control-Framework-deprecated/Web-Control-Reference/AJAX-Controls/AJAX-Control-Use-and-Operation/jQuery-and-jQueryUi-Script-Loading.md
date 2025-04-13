The AJAX controls in the Web Control Framework will automatically load jQuery, jQuery UI and ww.jquery.js (as needed) into the page with load options that allow you to specify where the script is loaded from.

The controls that automatically load scripts are (all are loading jquery.js and ww.jquery.js):

<ul>
* wwAjaxMethodCallback
* wwHoverPanel
* wwDragPanel
* wwModalDialog
* wwDatePicker (+jquery.ui)
</ul>

By default jquery.js and jquery.ui.js load from the Google CDN, ww.jquery.js loads from the scripts directory.

### Manually overriding Script Locations
Each of the controls have default load settings and I highly recommend that whenever possible you use these default settings because they can auto-update in the future. If you need to manually override the script locations you can do so easily by using the jQueryScriptLocation property on each control or the CalendarScriptLocation for jQuery.ui. Note that the first control rendered in the page (based on markup location) will determine where these scripts are loaded from.

You can also explicitly force supported libraries to load manually via code. This allows you complete control over the script load mechanism. Essentially the first time the load APIs are called are the ones that are applied. So calling the load API early in the page cycle in OnInit() will override any settings that controls on the page provide. So if you do the following you can control the load order *exactly*:

```foxpro
FUNCTION OnInit()

* ** Default setting - defaults to CDN
this.jQueryConfig.IncludejQuery()

* ** Explicitly specify a local URL
this.jQueryConfig.IncludejQueryUi("~/scripts/jquery-ui.min.js")

* ** Default setting - defaults to a local url (/scripts/ww.jquery.js)
this.jQueryConfig.IncludewwjQuery() 

ENDFUNC
```


These controls have load options that determine how these scripts are loaded. The default is "CDN" which loads jQuery and jQuery.ui from the Google CDN with an optoinal fallback to a local URL. 

**Content Delivery Network (CDN)**  
CDN mode loads jQuery and jQuery UI from the Google CDN if you are connected and falls back to a local URL if you're not connected or the connection is refused.

```html
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
   <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.4.2/jquery.js" type="text/javascript" ></script>
   <script type="text/javascript">
   if (typeof(jQuery) == 'undefined')
      document.write(unescape("%3Cscript src='http://ajax.googleapis.com/ajax/libs/jquery/1.4.2/jquery.js' type='text/javascript'%3E%3C/script%3E"));
   </script>
    <title>Default2</title>
    <link href="../westwind.css" rel="stylesheet" type="text/css" />
    
   <script src="/wconnect/scripts/ww.jquery.min.js" type="text/javascript" ></script>
   <script src="http://ajax.googleapis.com/ajax/libs/jqueryui/1.8.4/jquery-ui.min.js" type="text/javascript" ></script>
   <script type="text/javascript">
   if (typeof(jQuery.ui) == 'undefined')
      document.write(unescape("%3Cscript src='/wconnect/scripts/jquery-ui.min.js' type='text/javascript'%3E%3C/script%3E"));
</script>
</head>
```

**Explicit URL**  
You can