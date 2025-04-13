If set to .T. assumes that all dates in the data structure passed are UTC dates and the dates are not time adjusted for UTC time.

Set this flag if you have applications that naturally store dates in UTC format in the database. Since FoxPro natively has no support for telling a UTC date from a local date, there's no automatic way for wwJsonSerializer to detect the date type. 

This flag allows giving a hint as to what date format the data is stored in. Note that you can only have it one way or the other - if your date formats are mixed you're out of luck.