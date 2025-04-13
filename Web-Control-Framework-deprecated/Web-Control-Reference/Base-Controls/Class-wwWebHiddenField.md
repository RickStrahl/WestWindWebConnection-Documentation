The hidden field control is just a specialized text control that creates an invisible field in the HTML document.

```html
<ww:wwWebHiddenField runat="server" id="txtHidden" Text="hidden text" />
```

All field output is driven through the Text property. Please note that you should not set a Value property in the declarative code.