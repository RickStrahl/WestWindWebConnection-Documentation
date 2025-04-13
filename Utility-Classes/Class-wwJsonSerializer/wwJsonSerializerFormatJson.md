Produces pretty-formatted, indented JSON from an unformatted JSON string.

Formats a raw JSON string by breaking out each property on it's own line and indenting hierarchical levels for easier reading and display.

Generated formatted JSON looks like this:

```javascript
{
  "key1": "test value",
  "key2": "value 2",
  "key3": {
    "Address": "32 kaiea",
    "Phone": "123-123-1231"
  }
}
```