Resolves a relative path to a full path but also matches the proper case of the file on disk if it exists. Essentially does what `FULLPATH()` does, plus casing the file as it is on disk if it exists.

```foxpro
? GetFullPath("..\..\temp\POST.PDF")      && c:\temp\Post.pdf - file exists
? GetFullPath("..\..\temp\POSTsss.PDF")   && c:\temp\POSTss.PDF - file doesn't exist
```