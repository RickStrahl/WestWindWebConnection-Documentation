Routine that loads the jQuery library onto a page. Web Connection internally uses this routine to embed jquery into the page so if you always use this method jQuery will only be embedded once even if called more than once.

Generates the following output when using CDN:

```html
<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.js" type="text/javascript" ></script>
<script type="text/javascript">
if (typeof(jQuery) == 'undefined')
   document.write(unescape("%3Cscript src='/wconnect/scripts/jquery.js' type='text/javascript'%3E%3C/script%3E"));
</script>
```

When in Debug mode loads jquery.js
When not in Debug mode load jquery.min.js