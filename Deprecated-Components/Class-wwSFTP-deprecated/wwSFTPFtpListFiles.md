Gets a file listing for a remote folder and returns a collection of objects with the following properties:

* Name
* Length
* FileAttribute  (16 - directory, 128 - file)
* LastWriteTime
* IsDirectory

You can use this method instead of `aFtpDir()` of the base class, which is not supported by this SFPT implementation.

Make sure a connection is open by calling `FtpConnect()` before using this and other low level operations.