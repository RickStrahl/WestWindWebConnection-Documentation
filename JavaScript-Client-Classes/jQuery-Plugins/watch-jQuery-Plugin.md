﻿This small jQuery plug-in allows you to monitor changes to any DOM element's CSS styles or attributes and fire a callback in response to any change in the monitored styles or attributes.

You can specify an element and any number of CSS properties or attribute names you want to monitor and if any of them are changed you are notified of the change via a function delegate you provide. The function delegate receives a object with an array of property names and current values, plus an index for the one that triggered the change.

<div class="syntaxbox">$.fn.watch = function(options)</div>


### Usage
To hook up an element for monitoring in script code use:

```javascript
// some element to monitor
var el = $("#notebox");

// hook up the watcher
el.watch({
    // specify CSS styles or attribute names to monitor
    properties: "top,left,opacity,attr_class",

    // callback function when a change is detected
    callback: function(data, i) {
        var propChanged = data.props[i];
        var newValue = data.vals[i];

        var el = this;
        var el$ = $(this);

        // do what you need based on changes
        // or do your own checks
    }
});
```

You simply specify the CSS styles or attributes to monitor and then hook up a call back function, and wait for change notifications to come in.

Note that you can get quite a lot of notifications especially if you're monitoring things like opacity during a fade operation or top/left during drag operations, so you should keep the code in this function to a minimum.

### Syntax
The syntax uses standard jQuery plug-in behavior attached to an element selector:

```javascript
$("#Element").watch(options)
```

The watch plugin takes the following option parameters:

```javascript
var options = {
    // CSS styles or Attributes to monitor as comma delimited list
    // For attributes use a attr_ prefix
    // Example: "top,left,opacity,attr_class"
    properties: null,

    // interval for 'manual polling' (IE 10 and older)            
    interval: 100,

    // a unique id for this watcher instance
    id: "_watcher",

    // flag to determine whether child elements are watched            
    watchChildren: false,

    // Callback function if not passed in callback parameter   
    callback: null
}
```

The main required property to set is properties which can contain any CSS Style property (top, left, opacity, display etc.) or an attribute name prefixed by attr_ (attr_class,attr_readonly,attr_src etc.).

The other required property is the callback property which lets you specify a callback function called when one (or more) of the properties change. The callback function receives two parameters which is an instance of a data object that contains an array of the properties monitored and the latest values.

An implementation of the callback function looks like this:

```javascript
function changeCallback(data, i) {
    // data object and index into the arrays
    var changedProperty = data.props[i];
    var newValue = data.vals[i];

    // this is the element affected
    var el$ = $(this)

    //... do your logic
}
```

If you only care to be notified and you don't care about changed or updated values, you can ignore the parameters - which is actually quite common. In that case you can do your own checks in your code to find what's changed or update other properties as needed.

If you want to know which element caused the event to fire you can use the code shown above to retrieve the changedProperty and newValue for property.

<div class="notebox">Note that the change event tracking is turned off for the duration of the callback function execution to avoid recursive events firing causing a potential browser lockup. If you need to make changes that require updating the DOM that affect further change events, you should use setTimeout() to delay execution until after the callback has completed.</div>

### Example Usage
As an example consider you have a couple of HTML elements - two boxes and you want to slave one box to the other:

```javascript
<div class="container">
    <div id="notebox" class="notebox">
        <p>
            This is the master window. Go ahead drag me around and close me!
        </p>
        <p>
            The shadow window should follow me around and close/fade when I do.
        </p>
        <p>
            There's also a timer, that fires and alternates a CSS class every
            3 seconds.
        </p>
    </div>

    <div id="shadow" class="shadow">
        <p>I'm the Shadow Window!</p>
        <p>I'm shadowing the Master Window.</p>
        <p>I'm a copy cat</p>
        <p>I do as I'm told.</p>
    </div>
</div>
```

There are two boxes #notebox and #shadow and what we want to do is monitor changes on #notebox and affect the behavior of #shadow to keep #shadow tied and synched to the #notebox.

The following code monitors #notebox so we can tell when a monitored value is changed:

```javascript
<script>
var el = $("#notebox");
el.draggable();

// Also update a CSS Class on a 3 sec timer
var state = false;
setInterval(function () {
    $("#notebox")
        .removeClass("class_true")
        .removeClass("class_false")
        .addClass("class_" + state);
    state = !state;
}, 3000);


// Hook up CSS and Class watch operation
el.watch({
    properties: "top,left,opacity,attr_class",
    callback: watchShadow
});

// this is the handler function that responds
// to the events. Passes in:
// data.props[], data.vals[] and an index for active item
function watchShadow(data, i) {
    // you can capture which attribute has changed
    var propChanged = data.props[i];
    var valChanged = data.vals[i];

    // element affected is 'this'
    var el = $(this);
    var sh = $("#shadow");

    // get master current position
    var pos = el.position();
    var w = el.outerWidth();
    var h = el.outerHeight();

    // and update shadow accordingly
    sh.css({
        width: w,
        height: h,
        left: pos.left + w + 4,
        top: pos.top,
        display: el.css("display"),
        opacity: el.css("opacity")
    });

    // Class attribute is more tricky since there are
    // multiple classes on the parent - we have to explicitly
    // check for class existance and assign
    sh.removeClass("class_true")
        .removeClass("class_false");
    if (el.hasClass("class_true"))
        sh.addClass("class_true");
}
```

When you run this code you'll essentially see #shadow follow around the #notebox when dragged or moved, fade out when the #notebox fades. You'd also see the CSS class of #shadow changed every three seconds, in response to the change on #notebox.

Note that the code above doesn't actually rely on the parameters passed into the watchShadow function, but instead does its own checks to see what needs updating. In fact this code simply updates all relevant properties whether they have changed or not. While less efficient it allows for simpler code and depending on how much change you need to do on the DOM, this can be very fast regardless. Your mileage may vary. If you have larger changes you need to affect, using the specific property to update the UI might be more appropriate.

### Browser Support

This plug-in will work with just about any browser, as it has a fallback for legacy browsers using interval polling. Modern browsers use an efficient API to get notified of changes by the browser.

This plug-in relies on the MutationObserver API in modern browsers to detect change events on elements. This API is supported in all current versions of Chrome, Mozilla, Safari, Safari iOS, and Internet Explorer 11.

Older versions of IE (10 and older) and any old browser that doesn't support MutationObserver can still work by using an inefficient setInterval() polling mechanism. It works, but there's always a slight, configurable delay between events being detected and the callback executing. However this fallback should work in any browser and so provides backwards compatibility. You can adjust the polling delay (default is 10ms), but you'll want to be careful to not poll too frequently to avoid slowing down browser operation especially on slower/older devices/machines.