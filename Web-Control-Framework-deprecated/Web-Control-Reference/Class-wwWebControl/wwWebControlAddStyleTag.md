Adds or changes a style tag on the control's Style property. If you're chaning style properties in code it's recommended you use this method rather than directly changing the Style property manually.

This method checks for existing style tags and updates them or adds the style.

**Example:**  
```foxpro
o.Style = "color:green;height:20px;width:400px;border-color:black;border-width:30px;"

o.AddStyleTag("font-weight","bold")
o.AddStyleTag("color","cornsilk")
o.AddStyleTag("width","240px")
```

**Result:**  
color:cornsilk;height:20px;width:240px;border-color:black;border-width:30px;font-weight:bold;