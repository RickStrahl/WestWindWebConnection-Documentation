Sets the FoxPro Locale settings based on a Locale.

```foxpro
SET DATE TO (loLocale.cDateFormat)
IF loLocale.l24HourTime
   SET HOURS TO 24
ELSE
   SET HOURS TO 12
ENDIF

SET MARK TO (loLOcale.cDateSeparator)
SET POINT TO (loLocale.cDecimalPoint)

IF !EMPTY(lcCurrencySymbol)
   SET CURRENCY TO (lcCurrencySymbol)
ELSE   
   SET CURRENCY TO (loLocale.cCurrencySymbol)
ENDIF
```