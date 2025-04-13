Calls a SOAP Server method based on a WSDL description. Call AddParameter for any parameters you need to add to your Methods, then call this method with the name of the method.

This method simplifies calling a SOAP service and reduces the process to setting the parameters for the call and calling this method specifying merely the method and the URL to the SDL file.

If you call only a single method on the Web Service you can pass the WSDL url to this method. Otherwise it's recommended you use loSOAP.ParseServiceWSDL(), so that the service description can be reused.