Extracts a text value between two delimiters. You can specify two ending delimiters or let the end of a line be considered an ending delimiter.

You can also optionally retrieve the string with its delimiters.

> #### @icon-info-circle Visual FoxPro 9.0 STREXTACT
> Visual FoxPro 9.0 includes a `STREXTRACT` function that provides similar functionality and should be used whenever possible as it's native and faster. `STREXTRACT` was introduced in VFP 9 based on this `Extract` function but behavior is slightly different. `Extract` calls `STREXTRACT` whenever possible.
>
>Use this function over `STREXTRACT` if you need to have a 3rd alternate ending delimiter, or you optionally need to search to the end of the string without matching a delimiter.