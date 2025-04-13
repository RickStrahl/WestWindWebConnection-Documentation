The JSON class provided here is Douglas Crockfords Json2 library embedded into this library for old browser clients that don't support JSON *if* the JSON library is not natively available in the browser. 

This class also extends the native JSON interface to support date parsing both in browser standard ISO format (default) and Microsoft's custom date encoding format.

This class is not loaded if a browser that natively supports JSON is used as most modern browsers do. The interface to this JSON class matches native JSON classes. It is based on <a href="http://www.json.org/" target="top">Douglas Crockford's JSON</a> JavaScript code with a number of modifications to handle date formatting in ISO and Microsoft formats. The parse() method checks for invalid characters in the JSON string before parsing and disallows executable code.

```javascript
var person = { Name: "Rick", Company: "West Wind", Entered: new Date() };

var json = JSON.stringify(person);

//{"Name":"Rick","Company":"West Wind","Entered":""2009-01-03T18:10:00Z""}
alert(json);  

var copied = JSON.parseWithDates(json);
alert( copied.Entered  + " - " + copied.Name);
```

Dates are formatted in ISO date format by default.