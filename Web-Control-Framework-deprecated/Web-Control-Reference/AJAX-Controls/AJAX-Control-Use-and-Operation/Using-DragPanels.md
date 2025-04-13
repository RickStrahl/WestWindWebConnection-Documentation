The wwWebDragPanel control is a server side control that extends wwWebPanel and makes the panel draggable. Here's a panel that's being dragged:

![](IMAGES%5CWebControls%5CControls%5CDragPanelDragging.png)

The control works by assigning a DragHandleID - an area that is treated as the drag initiator when the mouse is held down - and using it to start a drag operation. A typical drag panel set up looks like this:

```html
<ww:wwWebDragPanel runat="server" id="DragPanel"  
                   DragHandleId="DragPanel_Header" closable="true"
                   CloseBoxImage="~/webcontrols/images/closebox.gif"
                   CssClass="blackborder" style="width:400px;background:white;" 
                   Width="500px" Draggable="true"
                   OnClientCloseHandler="OnPanelClose"                          
>
   <div id="DragPanel_Header" class="gridheader">Drag Test</div>
   <div id="DragPanel_Content" style="padding:20px;">
       Here's my content
       <input type="button" value="Check" onclick="SaySomething();" />
   </div>    
</ww:wwWebDragPanel>
```

Dragged content appears transparent while dragged. DragPanels can also be made closable which applies a close button to the upper right corner of the DragHandle.

In this panel there's a header that is used for the drag handle. When the mouse is clicked in the header and held down the panel instantly becomes draggable and be moved anywhere on the page. The second content panel is optional but typically if you add a header you'll want to add some padding to the content so you'll typically need a content container to make the panel look right.

Once a panel is dragged it becomes an absolutely positioned control, even if the original control was a flow layout based control. This means as you pick up a draggable control you may end up re-arranging the overall document flow. If you want to be sure there are no odd effects by making a control draggable, make the draggable control absolutely positioned from the start.

**Usage Recommendations:**  
<ul>
* Use panels with a background color- transparent backgrounds don't overlay well
* Use panel styles that have a border so the panel offsets
* Apply a shadow using client code: 
**new wwControl('DragPanel').showShadow(.85,8);** 
to help offset the panel once placed ontop of the page.
</ul>

### The Original Control Markup
When you drop a wwWebDragPanel control onto the page it will drop a template onto the page:

```html
<ww:wwWebDragPanel ID="WwWebDragPanel1" runat="server" CssClass="blackborder" Width="400">
    <!-- Drag Handle: Make sure to assign Unique ID and set matching DragHandleID -->
    <div id='DragPanel_Header' class='gridheader'>
        Header</div>
    <!-- Content Panel: Assign Unique ID -->
    <div id='DragPanel_Content' style='padding: 20px'>
        Content</div>
</ww:wwWebDragPanel>
```

This template is only a suggestion only and you can completely redesign it if you choose. As dropped the control IS NOT DRAGGABLE - you need to first assign a control ID and attach the DragHandleID. To hook up this layout you'll need to change it to the following:

```html
<ww:wwWebDragPanel ID="DragPanel" runat="server" 
CssClass="blackborder" Width="400" DragHandleID="DragPanel_Header">
    <div id='DragPanel_Header' class='gridheader'>Header</div>
    <div id='DragPanel_Content' style='padding: 20px'>
	  Enter your name: <ww:wwWebTextBox runat="server" id="txtName" /><br />
        <ww:wwWebLabel runat="server" id="lblPanelContent" />
	</div>
</ww:wwWebDragPanel>
```

The names of the contained panel IDs are entirely up to you - I recommend using the name of the panel plus some extensions to make it clear they are related, but it's up to you. Make sure you that change the content Id names if you add more than one DragPanel to the page or you'll end up with duplicate 

Notice that DragPanels are just special wwWebPanels. This means you can use them for composition as any other panel and embed complex control layouts inside of them and that content will work as part of the page as any other content. You can databind the content and update it from the server and through client code.

### wwDragBehavior Client Side Object
The wwWebDragPanel control interacts with the wwWebDragPanelBehavior class on the client which performs all the drag functionality. This client class is fully self contained and can be used independently of the server control. The client control can make any DOM element draggable.

```cs
var db = new wwDragBehavior('btnSubmit','btnSubmit');
```

This adds a drag behavior to a button and sets the drag handle to itself which means the button itself becomes draggable. 

### Closable Panel
wwWebDragPanels also have a closable property which only works if a DragHandleId is provided. If set the Closeable property creates a close button icon in the upper right corner of the DOM control specified by the DragHandleID. By default an image is used in ~/images/closebutton.gif. You can use the CloseButtonImage property to override the image. 

When closed the panel is merely made not to display (style="display:none"), so the actual panel is still there and alive and potentially posting data back to the client.