Function that replaces a format string with placeholder values passed as parameters using C-Style Format strings. This function is useful for quick embedding of strings that is more compact and efficient than using TEXTMERGE expressions on generating short text strings.

The format is using positional parameters as {0} {1} {2} embedded into the string.

Example:
```foxpro
lcName = "Rick"
? StringFormat("Hello {0}. The time is: {1}",lcName,DateTime())
```

Values embedded are embedded using the TRANSFORM () and converted to strings. If you require special formatting for values turn the values into strings and apply your own formatting:

```foxpro
lnPi = 3.14159265
? StringFormat("Pi has a value of {0} when truncated.",TRANSFORM(lnPi,9.999))
```

Note that - for now - unlike C or C# there are a no formats applied since you can just as easily apply the formatting to values passed into the function. Values are simply rendered using the default TRANSFORM() encoding.