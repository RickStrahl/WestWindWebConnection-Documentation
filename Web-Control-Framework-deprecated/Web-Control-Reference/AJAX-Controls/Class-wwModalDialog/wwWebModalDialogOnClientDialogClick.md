Client Event handler that is fired when anything inside of the dialog is clicked. 

The handler receives *this* as the element clicked and e and inst parameter for the event and modal dialog instance respectively. Return true to close the dialog, false to leave it open.

Here's an example of an implementation
```javascript
function modalClickHandler(e, inst) { 
                    // this = button or link clicked
                    if (this.id == "btnOk") {
                       showStatus("thank you for your feedback");    
                       return true;
                    }
                    if (this.id == "btnCancel")
                       return false;  // dialog is not closed
                    
                    // * ** don't close unless buttons where clicked
                    return false;
                });
```

Note that you can surpress closing of the window with a false return and if you have many controls in the dialog you almost certainly will have to implement this handler and ignore clicks from anything but the close buttons or whatever you use to close the dialog.