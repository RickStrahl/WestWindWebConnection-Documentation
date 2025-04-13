Maps a JSON property name to a new name. This can be useful if you created JSON from a FoxPro object, but the JSON needs a property name that FoxPro's property naming limitations don't allow for.

You can create JSON property names with spaces, dots, dashes or any other special characters or names that start with a number, all of which are not supported in FoxPro. You can basically map a FoxPro property name to **any other string value** all of which are supported in string delimited JSON map property names.

> Unlike [PropertyNameOverrides](VFPS://Topic/_3FY0SY7K1) which can only transform case of a property, the `MapPropertyName()` method allows you to map property names to **any string value** including those that FoxPro properties would not otherwise support.