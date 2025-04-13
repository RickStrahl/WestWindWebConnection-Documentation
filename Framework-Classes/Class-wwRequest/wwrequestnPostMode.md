Request property that gets set by InitializeRequest and determines what kind of Form submission is occurring.

0 - Not a POST operation
1 - UrlEncoded POST (standard HTML forms)
2 - Multipart Forms 
3 - XML mode

This property can be read after InitializeRequest is called.