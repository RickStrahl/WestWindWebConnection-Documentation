Works like $.serialize() to serialize form data into a POST comaptible string, except that it doesn't serialize any of the ASP.NET specific ViewState,EventValidation and various argument parameters.

<div class="syntaxbox">$.fn.serializeNoViewState = function()</div>

### Example
The following picks up the post data for a form and sends it to the server with ajaxRequest.
```javascript
var postData = $("form1").serializeNoViewState();
new HttpClient(ajaxRequest("Somepage.wcsx",postData,function(result) { alert(result););
```


**props**  

A comma delimited list of CSS style properties that are to be watched for changes. If any of the specified properties changes the function specified in the second parameter is fired.

**func**  
The function fired in response to a changed styles. Receives *this* as the element changed and an object parameter that represents the watched properties and their respective values. The first parameter is passed in this structure:

```javascript
{ id: itId, props: [], func: func, vals: [] };
```

A second parameter is the index of the changed property so data.props[i] or data.vals[i] gets the property value that has changed.

**interval**  
The interval for setInterval() for those browsers that don't support property watching in the DOM. In milliseconds.

**id**  
An optional id that identifies this watcher. Required only if multiple watchers might be hooked up to the same element. The default is _watcher if not specified.

### Example
The following code demonstrates how to keep a related element in sync with a primary element (here a shadow with a div). As the item moves, is resized or the display changes the related element applies the same operations:

```javascript
el.watch("left,top,width,height,display,opacity,zIndex",
     function(w, i) {
         if (el.is(":visible")) {
             $(this).shadow(opt);
             sh.css("opacity", el.css("opacity") * opt.opacity);
         }
         else
             sh.hide();
        
     },
     100, "_shadowMove");
```

### jQuery.fn.unwatch(id)
Unhooks watching of the element by disconnecting the event handlers.

**id**  
Optional watcher id that was specified in the call to watch. This value can be omitted to use the default value of _watcher.