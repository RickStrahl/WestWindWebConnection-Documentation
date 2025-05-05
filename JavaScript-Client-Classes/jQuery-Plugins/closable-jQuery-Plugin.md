This plug-in adds a clickable close box to the specified element. The closebox can be driven either by a CSS style (.closebox by default) that must be set in order for the close button to render or by providing an image link that provides the closebox image.

This plug-in works by inserting an absolutely positioned element into the the specified component or provided handle. The element in question is hidden when the close button is clicked and an optional handler is called when the element is 'closed'. Closing happens via jQuery.hide() or jQuery.fadeOut(if the fadeOut) parameter is provided.

<div class="syntaxbox">$.fn.closable = function(options) </div>

### Options
The options parameter can be a map of any of these properties (default values are shown):

```javascript
var opt = { handle: null,
	closeHandler: null,
	fadeOut: null,
	cssClass: "closebox",	 // closebox-container
	imageUrl: null
};
```

**handle**  
An optional HTML element into which the close box is drawn. 

Forces the selected elements to document absolute level. Use this option if the element doesn't center correctly which can happen if its position is relative already. When set the element is removed from its current hierarchy and moved to a child of body.

**closeHandler**  
A function that is called when the close button is clicked. The handler is called just before the window is closed and returning false from the handler will leave the window/element visible. Otherwise the window is closed. The handler passes the *this* pointer as the closable component.

**fadeOut**  
By default the window is closed with .hide(). If you'd like it to fade out use a fade option (slow,normal,fast or milliseconds) to specify the fadeout speed.

**cssClass**  
The CSS class that is responsible for rendering the closebox. Note that the default is closebox and if it doesn't exist you won't see a close icon since the style determines the default rendering. If no imageUrl is specified the close button is rendered as a <div> tag with this CSS class applied. Otherwise an <img> tag is rendered and the cssClass is applied.

The default class is "closebox" which by default is a small icon rendering inside of the top right of the closable container. The default styles also support "closebox-container" which renders a bigger icon that sits just outside of the box (lightbox style).

**imageUrl**  
If specified the image that is used as the close button. Not required if you use a cssClass that includes a background image which is the recommended way to hook up the close button. If specified cssClass applies against this img control.

### Recommended Styling for cssClass
cssClass can be set up to render a close button. If no imageUrl is specified a div tag is rendered and you can use a background image to display the close image:

```html
.closebox,.closebox-container
{
	position: absolute;    	
	right: 2px;
	top: 2px;
	width: 16px;
	height: 16px;
	background-image: url(/images/close.png);
	background-repeat: no-repeat;
	cursor: pointer;    	
	opacity: 0.80;
	filter: alpha(opacity="80");
} 
.closebox-container {
    right: -10px;
	top: -10px;
   	width: 28px;
	height: 28px;
	background-image: url(/images/closex.png);
}
.closebox:hover,.closebox-container:hover 
{	
	opacity: 1;
	filter: alpha(opacity="100");
}
```

Note this relies on a close.gif image that gives a red box with white X inside it but you can use any image you choose of course.