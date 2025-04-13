Basic number formatting features. Only a very limited of common decimal formatting options are provided for numbers are provided.

**c**  
Currency format. Use the Option parameter to specify the currency symbol (defaults to $). Numbers are rounded to two decimal places. 
* $1,200.12
$1,200.00
* 

**n or n2**  
Formats a number using standard locale number format including commas. An optional numeric value can follow n to specify the number of decimal places. 
* n: 1,212.21 
n4: 1,222.2100
n0: 1,220
* 

**f or f2**  
Displays numbers as raw numbers with decimal places. The f maybe followed by a number that specifies the number of decimal places to display.
* n: 1212.21 
n4: 1222.2122
n0: 1220
*