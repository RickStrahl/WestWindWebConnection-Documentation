The wwDragPanel is a specialized panel that can be dragged around the page and is optionally closable as an absolute positioned control. Draggable Panels can be very useful in AJAX applications that pop up additional 'windows' and display information in these windows. 

A typical drag panel layout looks like this:

```html
<ww:wwDragPanel runat="server" id="DragPanel"  
                   DragHandleId="DragPanel_Header" 
                   Draggable="true" closable="true"
                   CssClass="blackborder dragwindow" 
			style="display: none;width: 450px; "
                   OnClientCloseHandler="DoSomething"                          
>
  <div id="DragPanel_Header" class="gridheader">Drag Test</div>
  <div id="DragPanel_Content" style="padding:20px;">
     What's your name:<br>
     <ww:wwWebTextBox runat="server" id="txtName" />
     <input type="button" value="Check it out" onclick="SaySomething();" />
   </div>    
</ww:wwDragPanel>
```

The key to making a panel draggable is the DragHandleID property which points at the element that is used as the initiator of the drag operation when the mouse is held down. Typically you'll want to use a header panel as shown in the markup above, but you can assign the DragHandleID also to the panel itself which makes the whole panel act as the drag handle. The names of the IDs are up to you - there's no enforced pattern.

Keep in mind that dragging a panel will make it's positioning absolute and keep it that way. If you don't want to have your panel affect the relative page layout, start your panel off as an absolute positioned control by specifying style="position:absolute".

Since wwWebDragPanel inherits from wwWebPanel it's a standard Web Control container and can contain either static text or controls that are updated through server code.