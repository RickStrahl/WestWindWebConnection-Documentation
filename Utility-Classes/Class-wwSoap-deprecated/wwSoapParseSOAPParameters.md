Retrieves an array for all the parameters in a SOAP Request package.

**Array format:**  

[1] - Name of the parameter
[2] - Value of the parameter 
[3] - XML type of the parameter (if available)

The value will be converted to the proper type if XML type information is available as part of the SOAP package. Otherwise the value is returned as a string.

This method is typically used on the server only to parse out the parameters sent by the client. As such this is more of an operational method.