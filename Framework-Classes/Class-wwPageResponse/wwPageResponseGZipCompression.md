If set to .T. automatically encodes the Response output to GZip compressed format.  When set Web Connection automatically checks whether the client supports GZip compression and only compresses content if it does.

This routine adds the required Response header and compresses the output properly. 

This routine checks the Accept-Encoding header in an Assign method to the property, and then performs the actual header addition and compression as part of the Render() method.