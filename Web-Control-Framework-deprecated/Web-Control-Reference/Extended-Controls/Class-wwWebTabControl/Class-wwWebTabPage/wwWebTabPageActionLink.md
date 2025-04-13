The action that occurs when the tab is clicked. The default behavior is to switch the assigned page.

Supported modes are:

<ul>
* **"default" or empty**  
The tab is selected and the TabPageClientId is activated
*  **javascript:SomeScriptCode();**  
If the expression is prefixed with JavaScript this code is executed when the tab is selected. Note that you can call ActivateTab('divPage1') or ActivateTab(0) to select a Tab page.
* **A URL**  
You can specify a URL that moves the current browser instance to a new page. This can be useful for an exit link or for pages that share a common tab display across the top (for example in a user control).
</ul>