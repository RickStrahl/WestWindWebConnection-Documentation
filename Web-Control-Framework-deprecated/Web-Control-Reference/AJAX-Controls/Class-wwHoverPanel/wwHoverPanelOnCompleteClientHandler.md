Event handler fired just before the HoverPanel is rendered. 

The function called receives the result data and an instance of the HoverPanel client class. Return false to cause the panel to not be activated and displayed automatically (ie. you take over display). Return true to let the panel render as usual.

If you decide to return false you might manually display the panel at mouse position which can be done as follows:



```javascript
function hoverCallback(result,_I)
{
	   result.replace("www.west-wind.com","www.east-wind.com"
        _I.assignContent(result);
        _I.movePanelToPosition(_I.lastMouseLeft + _I.hoverOffsetRight, _I.lastMouseTop + _I.hoverOffsetBottom);
        _I.show();
	
}
this.displayResult = function(result) {
    if (_I.completed && _I.completed(Result, _I) == false)
        return;
    if (_I.eventHandlerMode == "ShowHtmlAtMousePosition") {
        _I.assignContent(result);
        _I.movePanelToPosition(_I.lastMouseLeft + _I.hoverOffsetRight, _I.lastMouseTop + _I.hoverOffsetBottom);
        _I.show();
    }
    else if (_I.eventHandlerMode == "ShowHtmlInPanel") {
        _I.assignContent(result);
        _I.show();
    }
}
```