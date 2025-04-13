Allows you to specify a JavaScript handler that is called when a page is activated. The handler is passed the tab index number.

The following JavaScript code demonstates the client handler which prevents page 4 (remember 0 based index) to be activated.

```javascript
function onClientActivate(index)
{
    alert( "Activated tab: " + index);
    if (index == 3)
    {
        alert("Tab 4 cannot be activated!" + TabControl[1].) ;
  
        // * ** return true as completely handled'
        return true;
    }

    // * ** Return false to continue to activate tab        
    return false;    
}
```

The return value determines whether the activation was handled by the function. Return true to not do default tab activation. Return false to continue to activate the requested tab.

Note that you can get a reference to the tab by using the name of the control plus an index: TabControl1[index]. Each client tab has the following struction:

```javascript
TabControl1[0] = {id: 0,tabId: 'Tabs_1',pageId: 'divInfo'};
```

where the tabId is the DOM id of the tab column, and pageId is the DOM id of the page content.