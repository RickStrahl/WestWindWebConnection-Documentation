A form helper function that simplifies setting HTML listvalues to server PostBack values with expressions. This function returns `checked="checked"` when the form value matches the provided value or an empty string if the value or form var is false.

This method aims to make it easier to use static HTML and reassign postback values more easily. Without this function the expression would have to use an IIF() function and the following code is a little easier to read and write:

```html
<input type="checkbox" name="IsActive" id="IsActive"
       <%= Request.FormChecked("IsActive") %>  />
```

The above will default to the first selected item when there's no postback or the form variable is not found. If you want to provide a preselected value then you can pass an optional model value:

```html
<input type="checkbox" name="IsActive" id="IsActive"
       <%= Request.FormChecked("IsActive",poParms.IsActive) %> />
```