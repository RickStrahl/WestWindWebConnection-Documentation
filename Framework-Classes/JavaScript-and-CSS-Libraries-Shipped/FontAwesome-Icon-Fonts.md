FontAwesome is a an icon library packaged as a Font. The CSS maps the fonts to standard CSS names.

**<a href="http://fortawesome.github.io/font-awesome/icons/" target="top">FontAwesome Icon Reference</a>**

Icons are accessed via simple CSS classes that use `fa` plus the `fa-iconName`. 

For example:

```html
<i class='fa fa-home fa-2x'></i>
<i class='fa fa-warning fa-2x'></i>
```
<i class='fa fa-home fa-2x'></i>
<i class='fa fa-warning fa-2x'></i>

One advantage of using fonts as icons is that they can be resized and styled using CSS:

```html
<i class='fa fa-check' class="color: green; font-size: 50pt"></i>
<i class='fa fa-warning' style="color: firebrick font-size: 50pt"></i>
```

<i class='fa fa-check' style="color: green; font-size: 50pt"></i>
<i class='fa fa-warning' style="color: firebrick; font-size: 50pt"></i>

Icons can be rotated and animated.

```html
<i class='fa fa-circle-o-notch fa-2x fa-spin'></i>
<i class='fa fa-spinner fa-2x fa-spin'></i>
<i class='fa fa-gear fa-2x fa-spin'></i>
```

<i class='fa fa-circle-o-notch fa-2x fa-spin'></i>
<i class='fa fa-spinner fa-2x fa-spin'></i>
<i class='fa fa-gear fa-2x fa-spin'></i>


### Usage
Add the following to your HTML head:

```html
<link href="~/bower_components/font-awesome/css/font-awesome.min.css" rel="stylesheet" />
```