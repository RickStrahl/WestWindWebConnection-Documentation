Assigns HTML content to the panel or more specifically the element specified in htmlTargetClientId.

This method can be useful if you manually intercept the callbackHandler events rather than letting the control do the positioning automatically.

**Example:**  
```cs
function OnCallback(Result)
{
	MyHoverPanel.assignContent(Result);
	MyHoverPanel.movePanelToPosition();
	MyHoverPanel.show();
}
```