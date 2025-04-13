A form helper function that simplifies setting HTML listvalues to server PostBack values with expressions. This function returns `checked="checked"` when the form value matches the provided value.

This method aims to make it easier to use static HTML and reassign postback values more easily. Without this function the expression would have to use an IIF() function and the following code is a little easier to read and write:

```html
<select id="OutputType" name="OutputType" size="1" class="form-control">
    <option value="Html" <%= Request.FormSelected("OutputType","Html") %> >Html</option>
    <option value="PDF" <%= Request.FormSelected("OutputType","PDF") %> >PDF</option>
    <option value="XML" <%= Request.FormSelected("OutputType","XML") %> >XML</option>
    <option value="JSON" <%=Request.FormSelected("OutputType","JSON") %> >JSON</option>
</select>
```

The above will default to the first selected item when there's no postback or the form variable is not found. If you want to provide a preselected value then you can pass an optional model value:

```html
<select id="OutputType" name="OutputType" size="1" class="form-control">
    <option value="Html" <%= Request.FormSelected("OutputType","Html", poParms.OutputType) %>>Html</option>
    <option value="PDF" <%= Request.FormSelected("OutputType","PDF", poParms.OutputType) %>>PDF</option>
    <option value="XML" <%= Request.FormSelected("OutputType","XML", poParms.OutputType) %>>XML</option>
    <option value="JSON" <%=Request.FormSelected("OutputType","JSON", poParms.OutputType) %>>JSON</option>
</select>
```