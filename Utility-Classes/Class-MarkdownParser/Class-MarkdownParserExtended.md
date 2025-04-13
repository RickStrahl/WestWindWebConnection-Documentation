Enhances the MarkdownParser classes with a couple of extensions for marking up code snippets, auto-link parsing and embedding Font-Awesome icons.

The extensions are as follows:

* **Code HTML Prefix and PostFix**  
You can use the `cCodeBlockStartHtml` and `cCodeBlockEndHtml` properties to specify the HTML that is used to specify a language. Use `###language###` in the string to replace the language specified in the markup.

* **FontAwesome Icon Support**
Allows specifying font-awesome icons using simplified syntax of `@ icon-icon-name` which makes it easier to embed icons into text.

```foxpro
loParser = CREATEOBJECT("MarkdownParserExtended")
loParser.cCodeBlockStartHtml = [<pre lang="###language###">] + CRLF
loParser.cCodeBlockEndHtml = [</pre>]
lcHtml = loParser.Parse(lcMarkdownText)
```