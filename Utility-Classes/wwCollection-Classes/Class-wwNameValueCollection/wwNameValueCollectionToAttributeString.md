Creates an Attribute string of key value pairs like that can be used for HTML elements. 

For example:

```foxpro
loCol = CREATEOBJECT("wwNameValueCollection")
loCol.Add("src","someimage.gif")
loCol.Add("valign","left")
loCol.Add("width","200")

lcHtml = "<img " + loCol.ToAttributeString() + " />"
```
produces:

```html
<img src="someimage.gif" valign="left" width="200" height="200" >
```