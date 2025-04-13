Writes an attribute value string that is properly encoded.

The result value ensures that values like Text=" "Some Value" " don't occur but rather are translated into Text=" &quot;Some Value&quot; ". Other encodings may be added at a later point