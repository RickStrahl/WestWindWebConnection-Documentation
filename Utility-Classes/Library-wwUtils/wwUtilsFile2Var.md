Function to read and write files to and from disk. Similar to `FILETOSTR()` and `STRTOFILE()` but it has additional features useful in busy server applications and certain application scenarios.

Unlike `STRTOFILE()` and `FILETOSTR()` in FoxPro, `File2Var()` is:

* Multi-user safe and provides for retries if a file is locked
* Automatically strips UTF-8 and Unicode BOMs from file content


Multi-user safe means that if the file is open for reading, it can still be written to in write mode. Likewise if the file is open in read or write mode you can still read the file.

BOM stripping removes the UTF-8 or Unicode pre-amble that many editors write to identify the document encoding. When reading a file from disk with `File2Var()` these BOM preambles are stripped off the document so you only receive the actual document content.