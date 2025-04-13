0 - display the raw evaluated value  
2 - input field with raw value embedded  
4 - input field with embedded <%= %> expressions (use for string captures)  

Field names match the table column or object property value.

### Example (using an object):

```foxpro
loRec = CREATEOBJECT("HtmlRecordConfig")
loRec.DisplayMode = 2
loRec.DataMode = 2


loObject = CREATEOBJECT("Empty")
ADDPROPERTY(loObject,"Name","Rick Strahl")
ADDPROPERTY(loObject,"Entered",DATETIME())
ADDPROPERTY(loObject,"Count",10)
ADDPROPERTY(loObject,"Active",.T.)

lcHtml =  HtmlRecord(loObject,loRec)
? lcHtml

ShowHtml(lcHtml)
```

Mode 0 - raw values

```html
<div class="record-container" >
<div class="row" >
   <label class="col-sm-2" >Active:</label>
   <div class="col-sm-10" >True</div>
</div>
<div class="row alternate" >
   <label class="col-sm-2" >Count:</label>
   <div class="col-sm-10" >10</div>
</div>
<div class="row" >
   <label class="col-sm-2" >Entered:</label>
   <div class="col-sm-10" >11/04/2018 12:54:01 pm</div>
</div>
<div class="row alternate" >
   <label class="col-sm-2" >Name:</label>
   <div class="col-sm-10" >Rick Strahl</div>
</div>
</div>
```


Mode 2 - input fields:

```html
<div class="record-container" >
<div class="row" >
   <label class="col-sm-2" >Active:</label>
   <div class="col-sm-10" >	<input type="checkbox" id="ACTIVE" name="ACTIVE" checked="checked"  />
	<label for="ACTIVE"></label></div>
</div>
<div class="row alternate" >
   <label class="col-sm-2" >Count:</label>
   <div class="col-sm-10" ><input type="text" id="COUNT" name="COUNT" value="10"  class="number"  /></div>
</div>
<div class="row" >
   <label class="col-sm-2" >Entered:</label>
   <div class="col-sm-10" ><input type="text" id="ENTERED" name="ENTERED" value="11/04/2018 12:53:02 pm"  /></div>
</div>
<div class="row alternate" >
   <label class="col-sm-2" >Name:</label>
   <div class="col-sm-10" ><input type="text" id="NAME" name="NAME" value="Rick Strahl"  /></div>
</div>
</div>
```

Mode 4 - `<% %>` expressions:

```html
<div class="record-container" >
<div class="row" >
   <label class="col-sm-2" >Active:</label>
   <div class="col-sm-10" >	<input type="checkbox" id="ACTIVE" name="ACTIVE" checked="checked"  />
	<label for="ACTIVE"></label></div>
</div>
<div class="row alternate" >
   <label class="col-sm-2" >Count:</label>
   <div class="col-sm-10" ><input type="text" id="COUNT" name="COUNT" value="<%= Request.FormOrValue("COUNT","poData.COUNT") %>"  /></div>
</div>
<div class="row" >
   <label class="col-sm-2" >Entered:</label>
   <div class="col-sm-10" ><input type="text" id="ENTERED" name="ENTERED" value="<%= Request.FormOrValue("ENTERED","poData.ENTERED") %>"  /></div>
</div>
<div class="row alternate" >
   <label class="col-sm-2" >Name:</label>
   <div class="col-sm-10" ><input type="text" id="NAME" name="NAME" value="<%= Request.FormOrValue("NAME","poData.NAME") %>"  /></div>
</div>
</div>
```