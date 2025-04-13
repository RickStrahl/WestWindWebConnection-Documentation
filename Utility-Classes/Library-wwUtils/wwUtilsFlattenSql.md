Flatten a SQL command that contains CHR(13) or CHR(10) characters into a single line by stripping out the CR/LF values and replacing them with a space.

Useful in code so you can write SQL expressions using multi-line SQL between `TEXT` and `ENDTEXT`.

For example:

```foxpro
TEXT TO lcSql NOSHOW
SELECT id, title, description
    FROM albums
    ORDER BY title
ENDTEXT

* &lcSql   && fails

lcSql = FlattenSql(lcSql)

&lcSql   && works
```