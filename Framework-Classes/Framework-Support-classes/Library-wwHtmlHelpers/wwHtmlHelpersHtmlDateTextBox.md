Creates a date entry control that allows automatic date capture and conversion. 

In auto modes the control will render a <a href="https://eonasdan.github.io/bootstrap-datetimepicker/" target="top">BootStrap DateTimePicker</a> or - on mobile devices - a native date time picker control. Values are retrieved properly regardless.

```html
<%
    *** To capture the values as dates
    ptDate1 = Request.FormDateOrValue("ptDate1",DateTime() - 86400)
    ptDate2 = Request.FormDateOrValue("ptDate2",DateTime() - 96000)  
%>

<div class="form-group">
    <label class="control-label" for="basicinput">Basic Date Input (auto)</label>
    <%= HtmlDateTextBox("ptDate1",ptDate1,[placeholder="Date 1" class="form-control"],0) %>
</div>

<div class="form-group">
    <label class="control-label" for="basicinput">Basic Date Time Input (auto)</label>
    <%= HtmlDateTextBox("ptDate2",ptDate2,[placeholder="Date 2" class="form-control"],4) %>
</div> 
```

### Rendered Examples

##### Date Time Picker (BootStrap DateTimePicker):  
![](IMAGES/HtmlControls/DateTimePicker.png)

##### Time Picker (BootStrap DateTimePicker):  
![](IMAGES/HtmlControls/DateTimePicker_Time.png)

##### Native (Chrome):  
![](IMAGES/HtmlControls/DateTimeChrome.png)

##### Native Mobile (iOS):  
![](IMAGES/HtmlControls/DateTimePickeriOs.png)