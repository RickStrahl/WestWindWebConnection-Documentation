Fired when a client selection is made in the AutoComplete dropdown.

This function is auto-implemented by default to stuff the value property of the selected object into the textbox. You can implement your own custom function.

```html
<ww:wwAutoComplete runat="server" id="txtSymbol"  OnClicentSelection="autoCompleteOnSelection"
                               ServerUrl="SymbolAutoCompleteCallback.wwd" />
```

Then in JavaScript code implement the handler:

```javascript
<script>
function autoCompleteOnSelection(evt, ui) {
   // assign the text box
   $("#txtSymbol").val( ui.item.value + " " + ui.item.exchange);
   
   // but now assign another value based on custom fields
   $("#divCompany").text( ui.item.company );
}
</script>
```

The handler receives an event object (in this case for the click) and a jquery ui object. The ui.item property holds the selected object which will include a label and value property plus any additional properties/fields your server side code might have generated when returning the object array.