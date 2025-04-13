This plug-in is based on the jQuery serialize/serializeArray method functionality and produces an object from Input Element Form Variables.

The object produced contains an object with properties for the names of each element and the value set to the value of the element as a string.

<div class="syntaxbox">$.fn.serializeObject = function()</div>

### Example
The following code demonstrates how to keep a related element in sync with a primary element (here a shadow with a div). As the item moves, is resized or the display changes the related element applies the same operations:

```javascript
var formVars = $("form").serializeObject()
alert(formVars.txtName + " " + formVars.txtCompany);
```