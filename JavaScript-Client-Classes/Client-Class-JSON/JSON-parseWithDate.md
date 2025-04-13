Parses a JSON string into a value or object.

The parsing operation checks for invalid characters and executable code in the JSON string. If that or actual eval of the JSON fails null is returned from the method call.

This method overrides either this instance of the JSON implementation, or a native version if it exists in the browser.