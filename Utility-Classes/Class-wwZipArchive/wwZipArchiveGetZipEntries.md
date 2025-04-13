Get a list of entries for the files contained inside of the zip file as a FoxPro Collection. Use this method to manually check the content of a zip file or selectively extract files.

The structure of the zip entries is:

* **Name** - Just the filename
* **FullName** - Full name including file relative path (if any)
* **Length** - Original file size
* **CompressedLength** - Compressive file size