Web Connection's databinding displays error messages on controls that are databound and provides links to those controls on the top of the page. This works great to jump to the control in error, but when that control lives on a tab page that is not visible you'll find that while the control gets activated the tab page won't automatically change to display it.

To work around this issue you can implement a JavaScript function [OnBindingErrorLink](vfps://Topic/_2BY0QZYF3) to intercept link clicks and activate the appropriate page explicitly. Whenever an error link is clicked this global function is called and you can use it to activate the appropriate page. The function receives a single parameter which is the element that has the error on it.

With this knowledge you can use a somewhat generic implementation to activate the appropriate page. The following code assumes a tab control named *Tabs* on which each tab page has a CSS class named *tabpage* assigned to it (ie. <div class="tabpage">): 

```javascript
function OnBindingErrorLink(control)
{      
    // * ** Find the parent 'tab' page id
    while(control)
    {    
        control = control.parentNode;
        if (!control)
            break;            
        if (control.className == "tabpage")
        {                        
            ActivateTab("Tabs",control.id);
            return;
        }
    }    
}
```
If you're using jQuery on the page this is even easier:

```javascript
function OnBindingErrorLink(control)
{      
   var jTab = $(control).parents(".tabpage:first");
   if (jTab.length < 1)
      return;
	 
   ActivateTab("Tabs",jTab.attr("id"));
}
```

The code walks backward from the control until it finds a the page that matches the tabpage CSS class and if found uses that element to activate the tab.

Both of these methods are generic so they should work if you add additional pages as long as they have the CSS class (tabpage) that you are looking for.