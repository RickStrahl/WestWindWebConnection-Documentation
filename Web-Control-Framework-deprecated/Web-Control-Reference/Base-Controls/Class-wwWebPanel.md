The wwWebPanel control is a container content control that can contain other literal content and other controls. You can also nest panels.


This control renders as a DIV tag and by default the DIV tag renders as an invisible container that shows no borders or background. The content however is visible based on the Visible or VisibleExpression properties. If you want the container to be visible you can set the BackColor and various Border properties.

Panels are great tools for creating content that can be hidden easily and dynamically both from client and server code. You can set the Visible property to .F. all child content is also not rendered. You can also dynamically force the panel to display or hide using the VisibleExpression which can be set in the designer.

**Example:**  
```html
<ww:wwWebPanel ID="Panel1" runat="server" Height="194px" Width="321px" 
                          CssClass="gridalternate" 
                          BorderColor="black" BorderWidth="2px"
                          BackColor="lightsteelblue" 
                          style="padding-left:10px;margin-left:1px">
        <br />
        Enter your name:<br />
        <ww:wwWebTextBox ID="txtName" runat="server" Width="263px">Rick</ww:wwWebTextBox><br />
        <br />
        Enter your company:<br />
        <ww:wwWebTextBox ID="txtCompany" runat="server" Width="263px">West Wind</ww:wwWebTextBox><br />
        <br />
        Enter your Address:<br />
        <ww:wwWebTextBox ID="txtAddress" runat="server" Height="94px" TextMode="MultiLine"
            Width="263px">32 Kaiea Place</ww:wwWebTextBox>
     <br />
     <ww:wwWebButton ID="btnShowData" runat="server" Click="btnShowData_Click" Width="135px" Text="Show Data" /><br />              
 </ww:wwWebPanel>
```