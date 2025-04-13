Creates an HTML Radio Button control.

To create a single radio button that acts like a checkbox you can use the following code:
```foxpro
? HtmlRadioButton("radRed","Color Red",true,"Red")
* <input type="radio" name="radRed" id="radRed" value="Red" checked="checked" /> <label for="radRed">Color Red</label>

? HtmlRadioButton("radBlue","Color Blue",false,"Blue")
* <input type="radio" name="radBlue" id="radBlue" value="Blue" /> <label for="radRed">Color Blue</label>
```

To create a radio button group use the same name and different values for each button in the group and give a unique id in the attributes.

```foxpro
HtmlRadioButton("radColor","Blue",true,"blue",[ class="radiocolor" id="radColor_1"])
HtmlRadioButton("radColor","Red",false,"red",[ class="radiocolor" id="radColor_2"])
HtmlRadioButton("radColor","Green",false,"red",[ class="radiocolor" id="radColor_3"])
```