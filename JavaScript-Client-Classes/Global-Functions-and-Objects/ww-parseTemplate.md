The parseTemplate global function  allows creating client side templates that are evaluated as Javascript. The template is like a client side ASP template using <# #=""> and <#= #=""> for expression evalution based on the data element passed in as a parameter.

The template parser turns the template into Javascript code that is executed.

Templates can be unobtrusively stored inside of the HTML document like this:
```html
<script type="text/html" id="script">
<div>
<b><#= content #><b>
<# for(var i=0; i < names.length; i++) { #>
   Name: <#= names[i] #> <hr />
<# } #>
</div>
</script>
```

And can then be called like this:
```javascript
var tmpl = $("#itemtempalte").html();  // grab the template content
var data = { content: "This is some textual content",
             names: ["rick","markus"] };

//assign the content with jQuery
$("#divOutput").html( parseTemplate(tmpl,data) );
```</#=></#>