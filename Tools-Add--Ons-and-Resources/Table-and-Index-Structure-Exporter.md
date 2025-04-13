This utility creates various useful text outputs from tables including CREATE TABLE, INDEX, a class var list as well as several different HTML form outputs from the data. 

This utility is useful for generating various lists and commands related to tables. The utility runs as a plain procedural program which you can run like this:

```
select * from TT_CUST INTO Cursor Customers
DO tools\CRT_DBF

ShowText(_cliptext)
```

which generates a CREATE TABLE statement for the above table. Output can be generated for the following types:

**TABLE** - Create Table Command  
**ARRAY** - Create Table using VFP's Array Syntax for large tables that exceed command lengths  
**INDEX** - Generates INDEX tag commands for recreating indexes  
**BOTH** - Generates table and index statements
**ARRAYBOTH** - Generates table and index statements using table   array syntax  
**FIELDLIST** - creates a comma delimited field list  
**CLASSVARS** - creates a comma delimited class property list with   type prefix  
**DOCUMENTATION** - creates a line break separate list of field names and type info  
**HELPBUILDER** - same as above but with Help Builder HTML formatting   
**HTMLEDITFORM** - creates an HTML Form with ASP Style template fields. See [GenEditRecord.prg](vfps://Topic/GenEditRecord.Prg) for more details.  
**WEBFORMEDITFORM** - Generates an HTML Table with Web Control Framework controls for each of the fields of the table mapping to the appropriate type of controls.  

All output is generated to the _clipboard from which you can paste into whatever program you need to.

In case you can't remember this list there's a front end form for this tool:

![](IMAGES\misc\CRT_DBF.gif)

To run the form:

```foxpro
DO FORM tools\CRT_DBF
```

This utility is extremely handy for use with your wwBusiness objects for the CreateTable and Reindex methods which can use the maintenance statements and apply them.

It's also a great way for quickly generating basic HTML forms for prototyping and pasting into your HTML editor.

This tool is also available from the Web Connection Tool menu.