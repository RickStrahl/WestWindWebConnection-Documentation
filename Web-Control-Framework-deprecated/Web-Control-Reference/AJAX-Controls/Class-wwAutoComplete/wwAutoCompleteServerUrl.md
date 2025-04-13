A URL on the server that returns the AutoComplete data.

The data returned from this URL must be JSON of an array of objects, with each object having a label and value property at least.

<pre>[{ label: "Microsoft Corp", value:"MSFT"},{label: "Intel Corporation",value: "INTC"}]</pre>

For more info on how to create server side output see [Implementing Server Side AutoComplete Functionality](vfps://Topic/_3C610KSR2).