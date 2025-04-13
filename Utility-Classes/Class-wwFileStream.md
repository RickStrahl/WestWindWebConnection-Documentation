The file stream class can be used to buffer large strings incrementally to file. This is one way to allow creating strings that are bigger than 16mb in size.

```foxpro
loFS = CREATEOBJECT("wwFileStream")

loFS.Write("Hello ")
loFs.WriteLine("Rick")

*** Large File
loFS.WriteFile("C:\installs\Distribution CD\Demos\wconnect.exe")

lnSize = loFs.nLength
lcText = loFs.ToString()
lcFile = loFs.cFilename  && GUID.txt

this.MessageOut("Size: " + TRANSFORM(loFs.nLength))
this.AssertTrue( lnSize = LEN(lcText) )

loFs.Clear()
this.AssertTrue( loFs.nLength = 0 )

loFS.Dispose()
this.AssertTrue( !FILE(lcFile) )
```