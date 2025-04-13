An optional client script event handler that can be fired when a page change occurs. 

The handler receives the page number that is about to be activated and should return true to indicate that the activate operation was completely handled and the original page change is not to occur. Return false or null to indicate for normal page activation logic to continue to fire.

A client handler should look like this.
```javascript
function ClientActivate(num)
{
    if (num == 3)
        return false;
    else 
        ActivateTab(3);
        
    return true;    
}
```
This example always forces the tab page to page 4 regardless of which tab is clicked.