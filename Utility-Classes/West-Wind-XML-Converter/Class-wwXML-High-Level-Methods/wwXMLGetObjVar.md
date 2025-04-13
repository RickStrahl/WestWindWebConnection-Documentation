Retrieves an element value from a tag expression in an XML document.

Assumes the XML document is a simple single 'record' object. In this case native code extracts the value of an XML item. Note: Only a single entry for the element searched for should exist. If multiple objects exist only the first instance is returned.

This method is meant as a light weight method to retrieve XML elements without requiring the XML parser and works well for objects persisted to XML as well as any other XML document that uniquely guarantees unique tag names.