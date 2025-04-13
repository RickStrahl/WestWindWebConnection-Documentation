Low level SOAP method that is meant to wrap all the SOAP packaging and call mechanism from the client side into a single call. 

This method's purpose is to make a raw SOAP call using whatever parameters were set. AddParameter should be called for all parameters before this method is called.

<blockquote>**Note:**  
If you are calling a WSDL based Web Service use [wwSoap::CallWsdlMethod](vfps://Topic/_0CL02VSH2) instead. This method properly handles WSDL type parsing and namespace references, which otherwise would have to be set manually.
</blockquote>