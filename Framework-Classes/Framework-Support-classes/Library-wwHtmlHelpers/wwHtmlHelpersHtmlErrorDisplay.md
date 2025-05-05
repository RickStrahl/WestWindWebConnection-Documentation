Creates an HTML error display 'bar' using the stock bootstrap Alert box that can be easily embedded into a page. 

Pass in a message along with an error icon to render the control, or leave the value empty and render nothing. You can also use an [HtmlErrorDisplayConfig](VFPS://Topic/_4GJ0LR1OQ) object instead of passing parameters.

### Example with multiple Parameters:
Embedding the control into a template is super easy:

```html
<%= HtmlErrorDisplay(pcErrorMessage,"warning") %>
```

Now if pcErrorMessage is empty nothing renders. But when the value is filled it display an error box:

![](/images/misc/HtmlMessageDisplay.png)

```html
<%= HtmlErrorDisplay(pcErrorMessage,"info",
                     [class="my-css-class"],
                     "Header",.t.) %>
```

![](/images/misc/HtmlMessageDisplay2.png)

Some of the formats and icons supported are:

* error, warning, danger
* info, success

`warning` is the default and you can use any of the Bootstrap alert styles available.

### Example with HtmlErrorDisplayConfig
You can also pass in a HtmlErrorDisplayConfig object instead of individual values:

```foxpro
* In Process method:
PRIVATE poError
poError = CREATEOBJECT("HtmlErrorDisplayConfig")

... logic that deals with input parsing

IF llIsValidationError
    *** Assign properties to the poError object
    poError.Message = "A validation error occurred..."
ENDIF

Response.ExpandScript()
```

Then in the HTML Template:

```html
<%= HtmlErrorDisplay(poError) %>
```