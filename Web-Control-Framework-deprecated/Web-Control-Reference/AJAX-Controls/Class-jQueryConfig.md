The jQueryConfig class holds default configuration information common script and CSS libraries. These default values are used by various controls in the Web control framework by default unless you override them explicitly.

Configures:
<ul>
* jQuery
* ww.jQuery
* jQuery UI
* jQuery UI CSS Theme
</ul>

The values from this class are used globally, using a public object. You can set defaults on this object like this:

```foxpro
loConfig = LoadjQueryConfig()  && Gets public instance
loConfig.jQueryCDNUrl =  "https://ajax.googleapis.com/ajax/libs/jquery/1.6.2/jquery.min.js"
loConfig.jQueryUiCDNUrl = "https://ajax.googleapis.com/ajax/libs/jqueryui/1.8.16/jquery-ui.min.js"
jQueryFallbackUrl = "~/myscripts/jquery.min.js"
jQueryUIFallbackUrl = "~/myscripts/jquery-ui.min.js"
```

You can set this code either globally in your Application's OnLoad() handler, or on a per Process class basis in the OnProcessInit() method of a Process class. If you use the latter make sure to do this in all your Process handlers.

For more detailed info please see [How jQueryConfig works](vfps://Topic/_3E30UM9T8).