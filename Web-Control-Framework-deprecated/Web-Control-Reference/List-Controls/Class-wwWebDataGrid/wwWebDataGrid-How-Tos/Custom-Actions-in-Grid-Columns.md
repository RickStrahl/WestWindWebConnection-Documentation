Frequently you might want to take custom actions in grid columns. A common scenario is deleting of records for example. You want to show an extra column in the grid and then perform some action that also affects the rest of the page.

The Grid doesn't support direct event routing as the actual embedded column expressions are just that: expressions rather than full controls capable of firing Postback events. You can hover easily create your own events. Let's look at the DeleteCustomer example specifically for a customer list grid.

There are two available approaches:

* Using Ajax Callbacks
* Using Postback Events to notify the current page

### Ajax Callback
This is what I've been doing recently because it's simply the easiest way to make especially delete calls, plus it avoids having to repaint the entire form (although you can still optionally force that to happen).

The first step is to add a column to the grid that will initiate an Ajax callback. I'll set up a JavaScript function called DeleteCustomer() which will take a Pk and the Event object for the click as parameters:

```html
<ww:wwWebDataGridColumn
    Expression="HREF([javascript:{}],[Delete],[ onclick='DeleteCustomer(] + trans(Pk) + [,event);' ])"
    HeaderText="Action"
    >
</ww:wwWebDataGridColumn>
```

Here I'm using the HREF() function from wwUtils to embed a hyperlink into the page and set its onclick event to call the DeleteCustomer() function we'll set up in the next step.

If you'd rather use a button you can use this expression:

```text
Expression=" [<input type='button' value='Delete' onclick='DeleteCustomer(] + trans(Pk) + [,event);' />] "
```

Next add an wwAjaxMethodCallback control to the bottom of the page (before the form tag):

```html
<ww:wwAjaxMethodCallback runat="server" ID="Proxy" />
```

and the following script code just after it to handle the DeleteCustomer call to the server:

```javascript
<script type="text/javascript">
    function DeleteCustomer(pk, event) {
        
        var ctl = $(event.target || event.srcElement); // capture link and turn into jQuery object

        Proxy.callMethod("DeleteCustomer", [pk],
                function(result) {
                    showStatus("Customer deleted. (Simulated record is not actually deleted on server)",4000);
                    ctl.parent().parent().fadeOut(1000);
                },
                function(error) {
                    alert("error: " + error.message);
                });
    }
</script>
```

This code will make the server callback to the server and pass the pk as a parameter. When the call returns with .t. or .f. a status message is displayed and hte item is removed from the grid. Note this uses jQuery .parent() function to walk up the tree to find the row and then hide that row. what's nice about it is that you don't end up completely reloading the UI - you only remove the item in question.

Finally you have to hook up the DeleteCustomer method in your form to handle the deletion. This is simple - just create a method called DeleteCustomer and have it accept the Pk (or whatever ID you embedded) parameter:

```foxpro
************************************************************************
*  DeleteCustomer
****************************************
***  Function: Delete Customer AJAX Callback Method
***    Assume:
***      Pass:
***    Return:
************************************************************************
FUNCTION DeleteCustomer(lnPk)

*** Code to delete customer here.

RETURN .T.
```


**Embed Control Behavior Using PostBackEvents**  
If you want to stick to pure postback code then you can use postback events. This is a little less work but more resource intensive than the previous approach as the page does in fact post back and you likely have to reload the data to display twice.

```html
<ww:wwWebDataGridColumn 
        Expression="HREF(this.Page.GetPostBackEventReference('this','DeleteCustomer',trans(pk),.T.),[Delete]) "
        HeaderText="Action"
    >
</ww:wwWebDataGridColumn>
```

Here you are basically embedding a hyperlink (HREF() function from wwUtils) and setting the link to the result from GetPostbackEventReference(). This function bascially gives you a piece of JavaScript code that initiates a postback event to the server. You specify the control that receives the event, and method on it that handles it, plus an event parameter.

On the page class you then implement the DeleteCustomer method like so:

```foxpro
************************************************************************
*  DeleteCustomer
****************************************
***  Function: Delete Customer Postback Method
***    Assume:
***      Pass:
***    Return:
************************************************************************
FUNCTION DeleteCustomer()

lnPk = VAL(this.GetPostBackEventParameter())

*** Code to delete customer here.
this.ErrorDisplay.ShowMessage("Customer Delete Fired with Pk: " + TRANSFORM(lnPk))

RETURN .T.
```

You have to retrieve the event parameter explicitly and convert it from a string. At that point you can delete your record. In most cases you're done as the grid will render with the data during the Render() operation - IOW, the removed record shouldn't show up.