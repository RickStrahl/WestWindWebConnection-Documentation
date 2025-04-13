Returns an JSON error respsonse that contains an object in the format of:

```javascript
{ "isCallbackError": true, "message": "error message" }
```

Note this same format is also used internally to capture exceptiosn from Callmethod which return this same error result by default.

Any calling code can override the default behavior by looking for LastException and customizing the JSON response.