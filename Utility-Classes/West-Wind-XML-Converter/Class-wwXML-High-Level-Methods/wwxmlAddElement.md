This method can be used to easily create fully encoded XML element strings. The method returns a string for adding an element such as:

```xml
<tag>value</tag>
```

including the trailing carriage return. You pass in a name and value and the value is encoded into the appropriate XML string.

Optionally pass in an indent for the element.