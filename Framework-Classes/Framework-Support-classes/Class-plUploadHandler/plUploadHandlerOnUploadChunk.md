This method is called when when a file chunk is uploaded. 

Generally you don't need to override this method unless you want to perform custom storage of the incoming file data.

This method should be overridden in a subclass to provide custom functionality. The default implementation writes out a file in the cUploadPath with the name the client provides.