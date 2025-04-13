A FoxPro Format expression applied to the value that is displayed.

This can be a FoxPro Format expression like @! or @YL or a function call. 

You can also use functions or methods for formatting by prefacing the name of a function/method with an = sign. However, the function MUST accept at least a single parameter (the value of the Expression) and return a string result.

**Examples:**  
<ul>
* =UPPER
* =TTOC         
* =TimeToC     
* =this.Page.FormatDate
</ul>
Note that you specify only the the = and function/method name not the () or parameter list. Web Connection will pass the specified function a single parameter which is expression value.

Note that if you use expressions it's often easier to simply create an Expression instead and simply pass the expression.