Optional client handler that is fired before the date pop up is displayed. Useful for updating the display characteristics of the dialog before it displays.

Note: This handler is also used internally to pop the dialog to the top of the zOrder. The zOrder assignment occurs before this handler is called so if you need to unset the z-Index to some non highest value you can do it here. 

```html
Enter a date: 
<ww:wwDatePicker runat="server" 
                 OnClientBeforeShow="BeforeShow"/>
```

And the handler:

```javascript
function BeforeShow(txtDateTextBoxElement) {
    $("#ui-datepicker-div").maxZIndex();
}
```