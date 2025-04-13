Comma delimited, lower case list of object properties that **should not** be serialized when serializing objects.

Any property name on this list is **not output into the JSON result**.

This property exclusion list is provided to avoid rendering FoxPro default base class properties into JSON output if you serialize any object that is derived from `Custom`, `Relation` or any other FoxPro base class, other than the  `EMPTY`.


> #### @icon-info-circle Not used on EMPTY Objects
> `EMPTY` objects (ie. `CREATEOBJECT("EMPTY")`) are treated differently and don't apply this property as it is not needed. Unlike other FoxPro base classes, the `EMPTY` class does not have any base properties, so no exclusions are needed and you can use any property name.
>
> This also applies to any Cursor and `Collection` serialization, which create their child objects based on the `EMPTY` class.
>
> Feature introduced in v7.33.

### Naming Conflicts and accidentally Ignored Properties
If you are using non `EMPTY` objects, it's possible that you can run into an inadvertant naming conflict where a property that is part of your object, is in the ignore list. For example, `Comments` or `Name` are quite common. 

If you have a FoxPro object that requires one of these names in its property list you can:

* Change to use an `EMPTY` class to serialize
* Provide a custom list of properties to ignore (or `STRTRAN()` out a single property name)




**Default Value:**  
activecontrol,classlibrary,baseclass,comment,docked,dockposition,controls,objects,controlcount,
class,parent,parentalias,parentclass,helpcontextid,whatsthishelpid,
width,height,top,left,tag,picture,onetomany,childalias,childorder,relationalexpr,timestamp_column