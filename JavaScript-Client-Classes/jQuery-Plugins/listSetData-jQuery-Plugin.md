Binds a list or dropdown control with object data from an array. The data can be a single value array, object array or data table (with a Rows array property). This function should be applied only to *select*   elements.

If no data is passed the list is cleared. By default the list is cleared unless the noClear option is passed as true.

<div class="syntaxbox">$.fn.listSetData = function(data,opt)</div>

### Example
The following example downloads data from the server and returns a table result and binds it to a dropdown list:

```javascript
function GetDeveloperList(clearList)
{    
    Proxy.callMethod('GetDeveloperList',[isElementInViewport],
            function(result) {
                
                // Bind company and pk fields
                $("#lstCustomerList")
                      **.listSetData(result,{ dataTextField: "company", dataValueField: "pk"});**  
           },onPageError);
}
```


**data**  
The data that is passed in in the form of a single item array, or an object array or a serialized table that has a Rows array. 

If this value is null or not passed the list is cleared.

**opt**  
Options that allow you to specify the dataTextField and dataValueField which should reference the JavaScript object properties.


**dataValueField**  
The value of the field that is the value

**dataTextField**  
The display text field that is displayed in the list. If omitted the value is used for display.

**noClear**  
By default the list is cleared before binding. Pass as true if you want to append the data.