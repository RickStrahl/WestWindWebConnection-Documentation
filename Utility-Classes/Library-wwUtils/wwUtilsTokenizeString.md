String tokenizing function that extracts and replaces tokens found by extracting value between a start and end delimiter. The extracted values are returned in a sequential collection.

If the source is passed by reference the source is modified with a tokenized placeholder.

Tokenized strings look like this (#@# delimiter):
**IEnumerable#@#1#@# List, Field field, List#@#2#@# fieldList**