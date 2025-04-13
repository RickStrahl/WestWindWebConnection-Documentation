Replaces the replace string or expression for any RegEx matches found in a source string 

NOTE: The behavior of this functionality is very different from native Replace behavior in most RegEx implementations. This routine basically finds matches and then uses FoxPro code to replace the values either with a string or an expression.

Example using Expression Replacement (converts the </h3><br> and </h2><br> to just a <br>):

```foxpro
lcText = "Here we go<h3>Header</H3><br>More Text <h2>Header again</H2><br>asasa"
? lcText

loRegEx = CREATEOBJECT("wwRegEx")
loRegEx.IgnoreCase=.T.

? loRegEx.Replace(lcText,[</h\d><br>],"STRTRAN(lcMatch,[<br>],[IncludewwjQuery])",.T.)
```

Note that the value **lcMatch** is available and can be used in the expression processing to indcicate the matched string value. Your expression can be anything that can EVALUATE() in FoxPro.