**moment** is a JavaScript time manipulation library that lets you display and convert date and time values to and from strings easily. Moment understands a lot of different time formats and can convert between them and can display time values in human friendly ways like today, an hour ago etc.

**<a href="http://momentjs.com/" target="top">Moment Documentation</a>**

### Examples
Moment has a large feature set, but here are a few examples that demonstrate some of moment's cool formatting features.

#### Format Dates
```javascript
moment().format('MMMM Do YYYY, h:mm:ss a'); // October 3rd 2015, 4:36:13 pm
moment().format('dddd');                    // Saturday
moment().format("MMM Do YY");               // Oct 3rd 15
moment().format('YYYY [escaped] YYYY');     // 2015 escaped 2015
```

#### Relative Time
```javascript
moment("20111031", "YYYYMMDD").fromNow(); // 4 years ago
moment("20120620", "YYYYMMDD").fromNow(); // 3 years ago
moment().startOf('day').fromNow();        // 17 hours ago
moment().endOf('day').fromNow();          // in 7 hours
moment().startOf('hour').fromHow()        // 37 minutes ago
```

### Usage
Add the following script to the bottom of your HTML document.

```html
<script src="~/bower_components/moment/min/momment.min.js"></script>
```

If you need localized versions:

```html
<script src="~/bower_components/moment/min/momment-with-locales.min.js"></script>
```