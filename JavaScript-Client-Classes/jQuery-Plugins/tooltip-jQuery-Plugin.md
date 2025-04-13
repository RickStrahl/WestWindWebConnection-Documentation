This simple plug in provides tooltip functionality on the selected elements. By default the tooltip display is a small yellow window much like the stock tooltip, but that can be activated through code. 

Highly useful for 'working' operators while loading data from the server via AJAX callbacks for example.

<div class="syntaxbox">$.fn.tooltip = function(msg, timeout, options)</div>

**msg**  
The message to be displayed. Can be text or html which is determined by the isHtml option.

**timeout**  
The timeout before the tooltip is hidden. 0 means don't hide (ie. manual removal).

**options**  
The default options that can be passed are defined as follows:

```javascript
var opt = { cssClass: "",
        isHtml: false,
        shadowOffset: 2,
        onRelease: null
};
```

The onRelease event handler is fired only if a timeout is set and is made in the context of the element. It receives an instance of the _ToolTip class.

**Simple Example:**  
```javascript
$("#txtName").tooltip("updating...");
... do something or async code
$("#txtName").tooltip("hide");
```

**onRelease Example:**  
```javascript
$("#txtName")
    .tooltip("updating...",5000, 
               { onRelease: function(tt) { 
                    alert('tooltip is closing on element: ' + this.id);
                 }  });
```