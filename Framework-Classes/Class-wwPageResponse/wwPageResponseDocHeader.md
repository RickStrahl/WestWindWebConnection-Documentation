Creates an HTML document header, <head> section and <body> tag.

**Example:**  
This FoxPro code:
```foxpro
Response.DocHeader("Hello World","css/westwind.css,css/myapp.css")
```

creates the following HTML document header:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Hello World</title>
    <link rel="stylesheet" type="text/css" href="css/westwind.css" />
    <link rel="stylesheet" type="text/css" href="css/myapp.css" />
</head>
<body>
```

You can also add additional head section content as a raw string. For example the following adds a couple of JavaScript incudes into the page:
```foxpro
lcScripts = ;
[<script src="scripts/jquery.js" type="text/javascript" ></script>] + CRLF +;
[<script src="scripts/ww.jquery.js" type="text/javascript" ></script>]

Response.DocHeader("Hello World","css/westwind.css,css/myapp.css",lcScripts)
```