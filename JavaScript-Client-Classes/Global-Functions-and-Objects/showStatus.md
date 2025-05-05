Global function that displays a status bar at the bottom of a Web page. The status bar can slide up from the bottom and slide down after a specified timeout to get out of the way. Alternately the statusbar can display in 'highlighted' mode and then revert to a less distinctive view or revert back to a fixed 'afterTimeoutText' message.

![](/images/jQuery-Statusbar.png)

The status bar can be formatted with CSS styling and the options allow configuring exactly which CSS classes are used.

### Operation
The statusbar can be used without any configuration simply by calling the above function at any time. The function will create the required <div> and add it to the document the first time the function is called.

```javascript
showStatus("Getting more data...");
```

Alternately you can configure the statusbar display once per page typically in $(document).ready():

```javascript
$(document).ready( function() {
     // one time configuration on page load
**   showStatus({ afterTimeoutText: "Ready", closable: true });**});
});

function someClickHandler() {
      // simple use multiple times anywhere in the page
	showStatus("Customer data updated.",4000);
}
```

### CSS Styling
The default styling provided looks like this:

```html
.statusbar
{
   position: fixed ;
   bottom: 0px;
   left: 10px;
   right: 10px;
   height: 16px;   
   padding: 8px 2px 8px 10px;   
   font-weight: normal;
   background: black;
   color: white;   
   opacity: .80;   
   filter: alpha(opacity="85");
   z-index: 200;
   overflow: no-display;   
   border-radius: 8px 8px 0 0;            
    box-shadow: -1px -1px 6px 2px #535353;
}
.statusbarhighlight
{
   font-weight: bold;
   color: cornsilk;   
    background: maroon;
}
.statusbarclose
{
   position: absolute;
   right: 1px; 
   top: 1px;
   color:red;
   font-size: 8pt;
   font-weight: bold;
      opacity: 0.80;
   cursor: pointer;
      width: 28px;
      height: 28px;
      background-image: url(images/closex.png);
      background-repeat: no-repeat;
}
.statusbarclose:hover
{
   opacity: 1;
}
```
