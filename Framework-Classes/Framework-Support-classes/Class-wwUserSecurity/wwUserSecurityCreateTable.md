Creates the User table if it doesn't exist. Called by the Open method.

To create a new table:

```foxpro
o = CREATEOBJECT("UserSecurity")
o.CreateTable()  && default table defined in `.cFilename`

o.CreateTable("someotherDb") && custom table
```

> #### @icon-warning Table Structure Change in v6.10
> The table structure of this table was changed in v6.10 and has to be adjusted in order for authentication to continue to work. Field sizes went to VarChar() and field data now needs to be trimmed.
>
> The table structure used is:
> ```foxpro
> CREATE CURSOR usersecurity ;
> (    PK          V (40),;
>    USERNAME    V (80),;
>   PASSWORD    V (80),;
>   FULLNAME    V (80),;
>   MAPPEDID    V (40),;
>   EMAIL       M ,;
>   NOTES       M ,;
>   PROPERTIES  M ,;
>   LOG         M ,;
>   LEVEL       Y ,;
>   ADMIN       L ,;
>   CREATED     T ,;
>   LASTON      T ,;
>   LOGONCOUNT  I ,;
>   ACTIVE      L ,;
>   EXPIRESON   D )
> ```   
> If updating from old versions make sure you replace all fields with TRIM()ed values.
>
> ```foxpro
> REPLACE ALL pk with TRIM(pk), ;
>            username with TRIM(username), password with TRIM(password), ;
>            MappedId with TRIM(MappedId)
> ```