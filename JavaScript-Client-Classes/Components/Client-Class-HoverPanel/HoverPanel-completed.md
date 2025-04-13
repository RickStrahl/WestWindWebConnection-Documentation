Optional client script handler called when the request completes. This event is fired just before the content is assigned to the panel and the HTML value of the response is provided as a parameter. 

Return false from this handler to NOT have the content displayed and no hover window to pop up. Anything else will continue to display the hoverpanel.

```cs
function ShowLineItems(event,Id)
{
    LineItemPanel.callbackHandler = CheckResult;
    LineItemPanel.startCallback(event,"id=" + Id + "&LineItemDisplay=1");
}
function HideLineItems()
{
    LineItemPanel.hide();
}
function CheckResult(result,hoverPanel)
{
    alert(result); 
    return false;    // abort display
}
```