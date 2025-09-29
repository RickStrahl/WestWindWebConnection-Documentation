Retrieves an HTML form variable posted to the Web server. Form variables are the primary interface for a Web client to communicate with a Web server and POST variables are the most common. Everytime a user on an HTML page fills out a form and submits it the values are POSTed to the server and can be retrieved with Request.Form("fieldname").

This method handles POSTs in the following formats which map to the nPostMode property which gets automatically set based on the Content-Type header. If the header is missing but there is POST data UrlEncoded is used (just in case).

* UrlEncoded (default - 1)
* Multi-Part (2)
* XML (3)

For XML mode posting a single XML Elements are looked for. 

If no parameter is passed to Form() the entire POST buffer is returned.