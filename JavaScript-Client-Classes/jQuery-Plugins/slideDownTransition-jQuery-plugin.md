This plugin is a replacement for the $.slideDown() plugin using CSS transitions for smooth and efficient sliding of content.

<div class="syntaxbox">$.fn.slideDownTransition = function()</div>

This plugin works by using a wrapper DOM element around your content and a couple of pre-defined styles to provide a slide transition. You can customize the transition speed and easing with the CSS.

### Requirements
First you need to specify two CSS classes:

```txt
.height-transition {
    -webkit-transition: max-height 0.5s ease-in-out;
    -moz-transition: max-height 0.5s ease-in-out;
    -o-transition: max-height 0.5s ease-in-out;
    transition: max-height 0.5s ease-in-out;
    overflow-y: hidden;
}

.height-transition-hidden {
    max-height: 0;
}
```

The wrap your content to slide up or down into a <div> tag that includes the height-transition style and optionally the height-transition-hidden style to hide the element initially.

```html
<div id="SlideWrapper" class="height-transition height-transition-hidden">
    <!-- content has to be wrapped so that the padding and
          margins don't effect the transition's height -->
    <div id="Actual">
        Your actual content to slide down goes here.
    </div>
</div>
```

Finally call the plugin functions to slideUp or Down:

```javascript
$("#Trigger").click(function () {
            
    if ($("#SlideWrapper").hasClass("height-transition-hidden"))
        $("#SlideWrapper").slideDownTransition();
    else
        $("#SlideWrapper").slideUpTransition();
});
```