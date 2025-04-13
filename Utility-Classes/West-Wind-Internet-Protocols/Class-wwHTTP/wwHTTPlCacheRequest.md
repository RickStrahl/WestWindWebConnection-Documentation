If set causes the current request to be cached if possible so that subsequent requests do not go back to the server to retrieve the data.

By default requests are forced to be refreshed on every hit and this option allows the current request to be stored into a cache and retrieved from the if found.

This feature is usually not desired which is why the flag is tuirned off by default, but can be useful in other scenarios, such as retrieving static XML documents such as WSDL definitions or XML Schemas that rarely change.