Displays a File Upload control on the form, which allows selection of a file from the local file system and uploading it to the Web Server.

This control automatically manages switching the form into multipart mode, so you don't have to explicitly change the enctype= of the form.

Note that unlike most other HTTP controls, this control does not post back its value automatically on the next page hit. IOW, when you post the page the file will be uploaded to the server, but when you return to the page the filename will no longer be shown/selected. Nor can you store or assign the filename of the control - HTML doesn't support this due to security concerns.

This class doesn't have any special properties to set other than stock formatting properties like CSSClass and Width etc.

To retrieve the file on the server, use loUploadControl.GetUploadedFile() or Request.GetMultiPartFile().


 which returns the binary data of the file and the filename:

```foxpro
lcFileName = ""
lcFileContent = this.UploadControl.GetUploadedFile(@lcFilename)

* ** Alternately get by form variable name
* lcFileContent = Request.GetMultiPartFile(UploadControl.UniqueId,@lcFilename)

* ** Make sure path to write to exists first!
STRTOFILE(lcFileContent,"d:\temp\uploads\" + lcFileName)
```