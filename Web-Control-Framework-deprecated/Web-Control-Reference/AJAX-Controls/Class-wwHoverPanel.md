The wwWebHoverPanel control provides an easy way to load and pop up windows with content from the server within the context of the current page. You can specify a Url and then with a single function call make the callback and populate the content of the hover panel and optionally pop it up at the server.

The server control works in tandem with the client side [wwHoverPanel class](vfps://Topic/_1WD05EXHW) which is automated by the server control.

Note this control inherits from [wwDragPanel](vfps://Topic/_1ZE15LCZI) and so inherits the ability to become draggable and closable if a DragHandleID is provided.

A typical markup layout looks like this:

```html
<ww:wwHoverPanel ID="LookupPanel" runat="server" 
              ServerUrl="~/webcontrols/SmallCustomerForm.wcsx" 
              EventHandlerMode="ShowHtmlAtMousePosition"             
              NavigateDelay="150"
              PostbackMode="Post"             
              ScriptLocation="WebResource"
              Style="display: none; background: white;" 
              PanelOpacity="0.89" 
              ShadowOffset="8"
              ShadowOpacity="0.18" >
    <!-- Note: These div panels are optional  -->
    <div id="PanelHeader" class="gridheader">Customer Info</div>
    <div id="PanelContent"  style="padding:10px;background:cornsilk;"></div>
</ww:wwHoverPanel>
```

By default the hover panel updates the panel's client content entirely. However, you can override the client ID that is the target of the client HTML update. In the example above the output is sent to the PanelContent <div> tag that's embedded inside of the hoverpanel. This allows you to design and style your panel independently of the output that's loaded into it from the server. A common scenario is for example to add headers as shown here.

To invoke a hover panel in client script, call the name of the panel and the startCallback function:

```cs
LookupPanel.startCallback(event,'id=' + $('txtCustomerId'));
```

where the second parameter is a (optional) querystring. Additional parameters allow a POST buffer and callback handler, and you can set additional properties on the client side LookupPanel object instance.

[Use and Operation](vfps://Topic/_1Q100DYWK)