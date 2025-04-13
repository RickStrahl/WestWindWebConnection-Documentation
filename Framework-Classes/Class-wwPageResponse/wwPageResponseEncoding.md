Sets the content encoding for the page for text content. 

By default no encoding is applied which means you are responsible for appropriately setting the headers and converting the encoding.

Possible values for this property include:
<ul>
* (no value set) - no encoding is applied
* UTF8
* UNICODE
</ul>

If no value is specified no encoding occurs and no headers are added. This leaves the browser to decide how to display the content unless you provide it.

Encoing is applied to the following content types only:

<ul>
* text/*   (anything that starts with "text/")
* application/javascript
* application/json
</ul>

Encoding occurs as part of the Render() method at the end of the request. If you specify either UTF8 or UNICODE the wwPageResponse class encodes the output as it's retrieved from the cOutput property.

You can specify this property to force output to be encoded into UTF8 and UNICODE which adds the appropriate Content-Type header and encodes the response into the appropriate format. 

This property causes the entire output to be encoded. Be careful with this feature if you have content that is already UTF-8 encoded in any way - for example if you have templates or WCF pages that are UTF-8 formatted. If these pages are already UTF-8 encoded you may end up double encoding. It's best to save all templates in OEM ANSI format.