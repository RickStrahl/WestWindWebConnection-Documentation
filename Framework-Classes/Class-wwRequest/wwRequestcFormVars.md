This property holds the raw POST buffer that the HTTP client submitted. 

The format of this buffer depends on the mechanism the client used to send the data to the server. Several modes are common (in order of occurrance):
<ol>
* Standard HTTP POST buffer containing UrlEncoded key value pairs
* Multipart HTTP POST data which contains multiple raw data sections
* Posted XML from a client such as Microsoft's XMLHTTP which ships with the XMLDOM parser
* Non-standard raw data. If client and server both are under your control you can simply send raw data over the wire. The Web server simply passes the data forward. To do so, use [wwIPStuff::AddPostkey](vfps://Topic/wwipstuff%3A%3Aaddpostkey)("",lcData)
</ol>

If you want to retrieve raw data such as a raw XML post, you can do so using the [wwRequest::FormXML](vfps://Topic/wwrequest%3A%3Aformxml) method.