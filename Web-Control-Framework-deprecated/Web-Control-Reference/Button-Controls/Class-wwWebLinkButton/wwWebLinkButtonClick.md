Occurs when the user clicks the link.

The click event should be routed to a method on the form. The event is designated in script code like so:

```html
<ww:wwWebButton runat="server' id="btnSubmit" value="Save" 
       click="btnSubmit_Click" />
```

You then need to implement the btnSubmit_Click method on your WebPage class:

```foxpro
FUNCTION btnSubmit_Click()
* ** Do something in response to click
ENDFUNC
```