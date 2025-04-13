This class provides easy to use pop up window functionality by calling back to a URL and retrieving the result content directly into a panel. Options allow for easy placement of the window and various simple effects like shadows and opacity of the window.

Note that this class can work without any server behavior - it simply requires some server URL to response with an HTML fragment. Note that the result should be an **HTML FRAGMENT** not a complete HTML page since the content is assigned to the innerHTML property of the panel.

To hook up a hover behavior:
```cs
<a href="somelink.htm" onmouseover="HoverTest(event)" onmouseout="HoverTestHide()">
Click me</a>
```

To make the calls:
```cs
// * ** Best to use a global var for the Hover Panel
// * ** Pass in the name of the HoverPanel Server Control and a URL
var Hover = new HoverPanel("Panel","InvoiceAbstract.aspx");
function HoverTest(event) 
{   
    Hover.navigateDelay = 400;
    Hover.adjustWindowPosition = true;
    Hover.eventHandlerMode = "ShowHtmlAtMousePosition"
    Hover.shadowOpacity = .10;
    Hover.shadowOffset = 10;
    Hover.hoverOffsetRight = 30;

    // * ** Start the actual callback: Event object, QueryString, PostData, ErrorHandler
    Hover.startCallback(event,"id=11074",null,OnError);
}
function HoverTestHide(event)
{   
    Hover.hide();
}
```