Returns the file attributes of a given file. The list of attributes is returned as a single string that contains all the attributes that apply.

For example to check for Read Only flag you might do:

```foxpro
IF AT("R",FileAttributes(lcOutputFile)) > 0 
	RETURN .T.
ENDIF
```