When using DataBinding you often use the BindingErrorsCollection.ToHtml method to bind errors to controls and to provide a link list to the error controls in the ErrorDisplay control. 

When using the BindingErrorsCollection.ToHtml() method to generate binding errors and the list of links that can be clicked to activate any of the controls there canbe a problem with controls that are current not visible on the page. A typical scenario is when Tab pages are used and the control(s) with an error are on a page that is currently hidden.

The client code that gets generated as part of .ToHtml() method includes a check for a special OnBindingErrorLink(element) function, that - if it exists in the page - is executed on any error link click. You can use this function to intercept clicks and based on the control passed in optionally activate the Tab page or otherwise make the control visible. 

You can return true from the function to indicate that you've handled the click completely and no further processing should occur. Otherwise the default behavior is still executed which will activate the link.

You can check individual controls:
```javascript
function OnBindingErrorLink(ctl)
{
    if (ctl.id == "txtTest" || ctl.id="txtName" || ctl.id=="txtAddress")
       ActivateTab("divPostBack");
    
}
```

Or you can use more generic code to capture entire groups of controls. The following example checks to see if a particular if the control lives on a TabPage (by checking for a specific ID name like TabPage_1) and then activates that tab before letting the default behavior activate the control:

```javascript
function OnBindingErrorLink(errorCtl)
{
    var control = errorCtl;
 
    // * ** Find the parent 'tab' page id
    while(control.parentNode)
    {    
        control = control.parentNode;
        if (!control)
            break;            
        if (control.id.substr(0,7) == "TabPage")
        {                        
            ActivateTab("Tabs",control.id);
            return;
        }
    }    
}
```

This is obviously more work than automatic activation but it gives you the needed control to activate and make visible the controls in question optionally. 

If the handler is missing no action is taken. This is a purely optional feature.