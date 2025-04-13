The wwXML class provides both high level and [low level methods](vfps://Topic/Class%20wwXML%20%28Low%20Level%20Methods%29). The high level methods create fully self-contained XML output documents and can import cursors and classes using this format by re-creating the object and cursor structures on the client end. 

The low level methods allow for creating and importing individual pieces of the XML documents.

> **Note**  
> XML is great for transferring data between applications and even intra application. But please realize that XML has inherent limations transporting some data such as binary content. Ctrl characters and CHR(0) are  not allowed and will fail on imports. While XML supports encoding at the node level this cannot be efficiently done for a generic utility such as wwXML. </blockquote>