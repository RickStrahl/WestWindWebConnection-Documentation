This method works like FILETOSTR() or FILE2VAR() to return the content of a file. The first time the file is read it's read from disk and then cached in a cursor. Subsequent requests for the same file (based on path + filename) retrieve the file from the cursor rather than from disk.

You can optionally check the file's timestamp to make sure that the file on disk hasn't changed.

Use this function instead of FILETOSTR() or FILE2VAR() in high volume scenarios where disk overhead becomes part of load issues on a Web site. Caching files in a cursor is 4 times faster than reading them from disk and saves considerable disk I/O overhead.