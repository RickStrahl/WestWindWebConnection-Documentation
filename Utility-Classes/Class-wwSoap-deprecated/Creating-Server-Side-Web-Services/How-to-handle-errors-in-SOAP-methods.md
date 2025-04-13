Error handling in SOAP and Web Services is not quite the same as a standalone application because of the stateless nature of each method call. In traditional stateful objects it's quite common to have an error condition in a method that is 'normal' - such as a validation routine failing a validation check and returning .F. with some other property holding the error conditions.

In SOAP this is not possible because you can't assign to properties and hope to retrieve these property values. There are a number of different approaches to handle these scenarios:
<ul>
* Only return simple values and ignore any error information
* Cause VFP ERRORs to occur which in turn result in SOAP fault messages that are passed to the client
* Pass back XML messages that contain the error information
* Pass back mixed data that contains XML of both content and error information
</ul>
The first option may work in some scenarios where you really don't care about the returned data or if there is a problem you can just let the user know and go on without correcting the issue.

Not a likely scenario though is it? <g> The second option is quite easy to implement and allows you to return a standard return value such as a boolean or string *and* also give error info back to the client application. To do this use code like this in the Web Service:

<pre>IF !loCustomer.Validate()
    ERROR "Validation failed: " + loCustomer.cErrorMsg
   RETURN .F.
ENDIF
</pre>

On the client this method can be picked up like this:

<pre>llResult = loProxy.Save()
IF loProxy.lError
    MessageBox(loProxy.cErrormsg)   && Show the message from above
   RETURN
ENDIF
</pre>

The XML returning scenarios involve that every request returns an XML document. This can be tedious, but you can build an easy error manager that can generate an XML error message block for you automatically. wwXML::CreateErrorXML() works fairly well for this, although you need to wrap this with a document element node to make valid XML.