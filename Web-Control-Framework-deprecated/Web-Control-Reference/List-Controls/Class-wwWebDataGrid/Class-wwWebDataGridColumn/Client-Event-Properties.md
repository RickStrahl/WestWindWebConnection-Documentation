Due to the way Visual Studio renders content in the designer any custom tags defined in on child controls cause the main control to fail to render. 

In order to facilitate a few common client scenarios, the data grid column also publishes a few Web Browser Events that are mapped to the column explicitly. While you can always use ItemAttributeString or a custom expression to add additional attributes to the colum cell definition it's often easier to do so with explicitly.

The following JavaScript events are surfaced as properties:
<ul>
* onclick
* ondblclick
* onmouseover
* onmouseout
</ul>