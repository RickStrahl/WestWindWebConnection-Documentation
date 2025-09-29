Creates a `<label>` control as an HTML string.

**Example:**  
```foxpro
lcHtml = HtmlLabel("Name:","txtName",[class="leftlabel"])
```

which generates: 

```html
<label for="txtName" id="txtName_label" class="leftlabel">Name:</label>
```