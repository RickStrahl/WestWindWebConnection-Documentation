Determines what type of result is expected.

Values include:
<ul>
* json
Value is JSON decoded and an object is returned to the caller. This is the default value.

* msajax
Server is MS AJAX style and returns JSON in a 'wrapped' format that has a message root object. When this option is specified callMethod picks up the 'real' object in the wrapper and returns only the result value/object.

* string
The result is a raw string and no decoding of any kind is performed.
</ul>