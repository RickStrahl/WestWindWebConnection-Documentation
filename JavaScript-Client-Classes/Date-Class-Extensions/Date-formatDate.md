Provides simple .NET like date formatting functionality.

Only basic formatting options are provided to allow for minimalistic simple date and time formatting.

```javascript
date = new Date().formatDate("mm-dd-yy") // 12/01/01
date = new Date().formatDate("yyyy-mm-dd HH:mm")  // 2001/12/01 23:59
date = new Date().formatDate("mm/dd/yyyy hh:mmt") // 12/01/2001 11:59pm
time = new Date().formatDate("hh:mm:ss t) // 11:59:22 pm
date = new Date().formatDate("MMM dd, yyyy") // Aug 08, 2010
```