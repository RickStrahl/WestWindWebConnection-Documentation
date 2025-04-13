The AutoComplete control provides a wrapper around the jquery-ui AutoComplete control. It provides automatic jQuery script and style loading as well as an easy way to implement the callback handler either as a JSON Service method or directly as a CallbackHandler in the Page.

![](IMAGES%2FWebControls%2Fautocomplete.png) 

The control works in combination with a server side handler that provides the JSON data to the control. The data can come from anywhere, but it must conform to the following rules:

<ul>
* Returns an array of objects
* Each object must contain a label and value property
* Additional properties may be on the object and are returned on selection
* JSON Example:
**[ { label: "Micorosoft Corp", value:"MSFT"},{ label="Intel Corpo.", value: "INTC" }]**  
</ul>

For more information on how to implement the server side code see [Implementing Server Side AutoComplete](vfps://Topic/_3C610KSR2)