Checkboxes in grids are not easy to handle for a number of reasons.

* Checkboxes tend to not be read only
* They render 'complex' html

HtmlDataGrid just takes plain data and renders it as an HTML table and so it doesn't natively deal with custom controls, but with a little extra work you can render and capture clicks.

#### Rendering
To render checkboxes you need to explicitly inject the checkbox HTML into the column. To do this you need to use explicit column generation rather than simply the simple `HtmlDataGrid()` function. 

For more info how to create a grid with explicitly added columns see:
* [Code Example for create a fully custom Data Grid](http://west-wind.com/webconnection/docs/_37x04bemx.htm)

To add a checkbox you might use code like the following:

```foxpro
loConfig.AddColumn("HtmlCheckBox([chkIsActive_] + TRANSFORM(Pk) ,[],Year > 2000,;
                   "class='with-font checkbox-activestate')",;
                   "Recently Produced")
```
which produces something like:

![](http://support.west-wind.com/PostImages/2016/_4N50O1E7F.png)


Notice that you should generate a unique ID for the name by combining some sort of identifier with the name (ie. `chkIsActive_445` which combines the name plus a key).

I'm also adding an explicit style `checkbox-activestate` so that I can find the element when capturing the content using CSS (see below).

#### Capturing
Rendering is one thing, but capturing the check is quite another. 

If you need to capture the check selection you typically have either:

* Capture the click and update the server immediately with an AJAX call
* Handle all selections visible when the form is submitted


##### Capturing with AJAX
To capture a click immediately as it's made you can use JavaScript and an AJAX call to push the data to the server, which can then save the data. To hook this up:

```javascript
$(".checkbox-activestate").click(function() {
    var id = this.id.replace("chkActive_","");
    
    // AJAX call to the server and pass ID and Value
    $.get("checkboxclick.wwd",{ id: id, value: this.checked } );
})
```

You can then handle the callback in a Web Connection handler:

```foxpro
FUNCTION checkboxclick()

lnId = VAL(Request.Form("id"))
llValue = Request.Form("value") == "true"

* ... do what you need to do to update your data
ENDFUNC
```

##### Capturing with a form POST
To capture a whole list of checkbox variables you need to insure that the checkbox values are contained within an HTML `<form>` tag and that the form can be submitted somehow.

In your server side capture code you can access each of the checkbox values like this:

```foxpro
FUNCTION CaptureCheckboxForm

loVars = loRequest.GetFormVarCollection()
FOR lnX = 1 to loVars.Count
   loVar = loVars[lnX]
   lcKey = loVar.Key
   
   IF StartsWith(lcKey,"chkActive_")
      lnId = VAL(STRTRAN(lcKey,"chkActive_",""))
      llValue = loVar.Value == "true"
      
      * Do something with the values
   ENDIF      
ENDFOR
```