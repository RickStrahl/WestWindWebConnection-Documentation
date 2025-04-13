The plUpload handler class is a Web Connection server backend for the plUpload component. plUpload allows uploads of multiple files and can support uploads of files larger than the 16 megs that Visual FoxPro's string limit imposes on Web Connection.

plUpload provides a simple facade for plUpload on the server side that can be implemented with very little code and almost no knowledge of the protocol that plUpload uses internally.  The handler class provides a simple, self-contained implementation that uses the Web Connection intrinsic objects to handle plUpload requests and routes to various 'events' like OnUploadStarted(), OnUploadChunk() and OnUploadComplete() that notifies your Web Connection application.

Typical implementations that upload to a file on the server only need to implement the OnUploadComplete event or the cOnUploadCompleteFunction pointer to provide an implementation for what happens when the upload completes.

For more information please see:

[How plUploadHandler works](vfps://Topic/_3QJ1BPFLN)