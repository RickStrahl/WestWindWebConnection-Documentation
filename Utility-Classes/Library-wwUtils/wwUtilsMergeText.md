This function provides an evaluation engine for FoxPro expressions and code blocks in a template/script page. Default syntax format uses ASP style tags (<% %>), but can work with any set of delimiters.

This parsing engine is faster than TEXTMERGE and provides extensive error checking and the ability to run dynamically in the VFP runtime (ie uncompiled). Embed any valid FoxPro expressions using

<pre>   <%= Expression%></pre>

and any FoxPro code with

<pre>   <% CodeBlock %></pre>

Expressions ideally should be character for optimal speed, but other values are converted to string
automatically. Although optimized for the delimiters above you may specify your own. Make sure to set the llNoAspSyntax parameter to .t. to disallow the = check for expressions vs code. If you use your own parameters you can only evaluate expressions OR you must use ASP syntax and follow your deleimiters with an = for expressions.

MergeText evaluates each code block individually and you cannot spread structured statements across multiple code blocks. For more complex scripts that compile and run as a single FoxPro PRG file please check out the [RenderAspScript()](vfps://Topic/_1VU0VSBIZ) function.