Takes a single file (DBF or otherwise) or a DBF/MEMO pair and encodes it into a string. String includes a header for verification and information about the file. Use DecodeDbf() to restore the string to a file.

> ##### @icon-warning Important!
> The file to be encoded cannot be in use.

> Note: This is a static function not a class method