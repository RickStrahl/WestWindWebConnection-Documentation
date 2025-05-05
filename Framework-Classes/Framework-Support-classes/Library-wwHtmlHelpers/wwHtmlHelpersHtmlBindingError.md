This function renders an error message based on a control id and validation error collection. If a validation error is found for the control the error is rendered.

Add this control after the control that it is displaying the error for.

```html
<div class="form-group form-horizontal">
    <label class="col-sm-2">Billing Rate:</label>
    <div class="col-sm-7">
        <%= HtmlTextBox("BillRate",poCustomer.BillRate,[class="form-control"])  %>
        <%= HtmlBindingError("BillRate",poErrors) %>
    </div>
</div>
```

If there's an error in the billrate text field unbinding process, or you've explicitly added some validation errors to the error collection, the HtmlBinding error is displayed. If no matching error is found or the error collection is null nothing is rendered.

Here's what a rendered error looks like by default:

![](/images/stepbystep/HtmlBindingError.png)