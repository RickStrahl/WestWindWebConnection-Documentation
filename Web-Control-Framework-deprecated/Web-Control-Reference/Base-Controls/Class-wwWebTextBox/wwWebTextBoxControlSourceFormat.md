FoxPro Format String used when a control source is bound to data. Also support a format method.

You can use standard FoxFormat expressions like "@!" for uppercase, or "@YL" for a long date value. You can also use InputMasks like "999,999.99" for numbers. Stock Fox behavior.

In addition you can also use Format methods which must take a single value as input and return a string result as output. The function or method must be prefixed with an = sign.

For exampe to format a data you might use: "=TimeToC" which is wwUtils UDF function. You can also use methods in the current page: "=this.Page.FormatDate()".

Note that this feature has tremendous potential for allowing you to dynamically assign content to controls. The same behavior is also used for DataGrids and essentially allows you to format each grid cell dynamically.

<blockquote>**Unbinding Note:**  
If you're using format strings for input fields, you may run into problems saving the data back on the server. If you unbind and use a format that cannot be converted back into the original format (say a string that's formatted: "(-999.99)" ) the unbinding will fail. So be mindful of the two-way conversion that needs to take place</blockquote>