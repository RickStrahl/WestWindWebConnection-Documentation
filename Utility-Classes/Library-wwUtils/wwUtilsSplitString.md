Splits a string on CR and LF breaks and a particular width and returns a collection. 

This function uses ALINES[] to split breaks, and then falls back to MEMLINES() for lines longer than the specified width (255 by default). 

Important for code parsers that rely on strings that don't exceed FoxPro's 255 string literal limit.

```foxpro
lcText = REPLICATE("These are some lines of text, not very long." + CHR(10),3) +;
                   CHR(13) + REPL("Final Return",30)

loLines = SplitString(lcText,255,0,[CHR(13),CHR(10)])  

? loLines.Count   
*** 6 - last line over 255 chars long so breaks

FOR lnX = 1 TO loLines.Count
   ? loLines.Item(lnX)
ENDFOR
```