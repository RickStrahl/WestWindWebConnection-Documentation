Parses a JSON string into a JavaScript value or object. This version throws an exception if parsing fails.

The parsing operation checks for invalid characters and executable code in the JSON string and throws an exception if either the pre-parsing or actual eval of the JSON string fails.

This version of .parse() is compatible with the JSON browser object implementatation which means it does not automatically parse dates into date objects. Instead all dates are returned as ISO date strings. Use JSON.parseWithDate() to automatically convert dates, or use JSON.dateStringToDate()