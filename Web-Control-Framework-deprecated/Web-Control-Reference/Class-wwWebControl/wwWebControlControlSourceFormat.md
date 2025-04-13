FoxPro Format String applied when a control source is bound to data. Expressions can be stock FoxPro format strings or any FoxPro or custom function

**FoxPro Format Expressions**  
You can use standard FoxPro Format expressions supported by the FoxPro TRANSFORM() command. The following are all valid format expressions:

```html
ControlSourceFormat="@!"
ControlSourceFormat="999,999.99"
ControlSourceFormat="@YL"
```

**Format Functions**  
You can also use a FoxPro function, UDF or class method as format functions by prefacing the ControlSourceFormat value with an =. The function must accept at least 1 input parameter (the value) and return a string.  The following are all valid format expressions:

```html
ControlSourceFormat="=TimeToC"
ControlSourceFormat="=DisplayMemo"
ControlSourceFormat="=this.Page.FormatDate"
```

This calls the TimeToC function with the ControlSource value as a parameter. The function MUST take a single input parameter that is the control source value and return a string as a result. Implicitly this translates to lcValue = EVALUATE("timeToC(lvValue)") where lvValue is the evaluated ControlSource value (which can be non-string). 

This feature has tremendous potential for allowing you to dynamically assign content to controls. Unlike using the ControlSource for formatting you can use this property and still be able to use the ControlSource effectively for unbinding back into the source value.

<div class="notebox">**Unbinding Note:**  
If you're using format strings for input fields, you may run into problems saving the data back on the server. If you unbind and use a format that cannot be converted back into the original format (say a string that's formatted: "(-999.99)" ) the unbinding will fail. So be mindful of the two-way conversion that needs to take place.</div>