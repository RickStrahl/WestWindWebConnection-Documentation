A collection of Attributes that are to be rendered on the control. Any attributed added is rendered into the HTML of the control.

For example, to add a custom client side JavaScript script handler you can use:
```cs
this.Attributes.Add("OnClick","ClientClick(this.value);")
```

You can use this for any attribute you might need to add.