Use this function to globally replace the JSON date parser with a data aware JSON parser. Rather than returning dates as ISO (or MSAjax) strings, this replacement converts these date strings into native JavaScript dates.

This method can be used to replace the global JSON parser on the page, with the date aware version of the JSON parser implemented by this custom JSON class. This function essentially replaces the default JSON.parse(json) method with JSON.parse(json,JSON.dateParser) which parses the JSON content for ISO or MS Ajax dates and converts them properly into JSON dates.

Use this function to globally replace the JSON parser, including for components like jQuery, Angular etc. that automatically parse JSON for you internally. Because this globally replaces the JSON parser, all use of the JSON parser is affected including those third party libraries. 

It's a great way to provide a simple setting to apply true dates in your JSON in one place.