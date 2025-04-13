Web Connection 7.0 converts a number of previously visual classes to PRG classes. The following visual classes were converted to `.prg` files in v7.0+:

* wwBusiness -> wwBusinessObject
* wwXML
* wwSql
* WebServer

VCX classes are a pain to manage in source control requiring extra tooling for conversion. They are also a pain to load in the Shareware version app file, and most of all they are a pain to access and edit using the FoxPro class code editor. Finally it's a pain for source control requiring a conversion step to export and import from an XML format. I much prefer working in PRG files, and these days I often edit my FoxPro code outside of FoxPro in Visual Studio Code which is not possible with VCX files. This is especially useful in light of the new Live Reload features in v7.0.

Now there's **only one visual class left**: `wcVisual.vcx`. This class library contains the actual visual components related to Web Connection (the web connection form, status form, the timer and few support dialogs etc.). The Console also has a few visual components left for forms. Everything else in the framework is now a `.prg` based class. Yay!

### Changing to PRG Classes from VCX Classes
For most of these classes the upgrade process is simply a matter of converting the `SET CLASSLIB TO` to `SET PROCEDURE TO` directives. The class names and APIs stayed the same so other than that change there should be no incompatibilities.

I recommend two ways to do this:

* **Search and Replace in Files** using a quality code editor like Visual Studio Code, Sublime etc. and change `SET CLASSLIB TO <lib>` references and change them to `SET PROCEDURE TO <lib>`
* **Use <a href="https://github.com/mattslay/GoFish" target="top">GoFish</a>** and manually find all `SET CLASSLIB TO <lib>` references and change them to `SET PROCEDURE TO <lib>`

This does end up causing some breaking changes, however I believe for most applications these changes are mainly search and replace changes. 

In most cases you should be able to do a search and replace for `SET CLASSLIB TO <class>` to `SET PROCEDURE TO <class>`.

#### wwBusiness turns into wwBusinessObject
`wwBusiness.vcx` is an exception to the simple conversion from Class library to prg, in that it is commonly used on forms and subclasses as a VCX. Since a VCX can't inherit from a PRG file (but vice versa works), I've left `wwBusiness.vcx` and the original `wwBusiness` class as is, and created a new class `wwBusinessObject` that lives in the PRG file instead. This allows using the new class and the old class in combination without naming conflicts. Functionality of both classes are identical, although the old class will see no future updates, while the new class might. Along the same lines the PRG library includes a `wwBusinessCollectionList` class for the child items list. 

To replace `wwBusiness` classes then is a two step process (if you use PRG classes already which you hopefully do as I do):

* Use VS Code or other quality Editor to Search and Replace in Files
* Replaces `SET CLASSLIB TO wwBusiness ADDITIVE` with `SET PROCEDURE TO wwBusinessObject ADDITIVE`
* Replace `wwBusiness` with `wwBusinessObject`

For VCX based classes you'll need an extra step **if you decide to convert and remove the old wwbusiness.vcx dependence**:

* Open up the Class Browser (FoxPro **Tools->Class Browser**)
* Find any of your VCX wwBusiness Subclasses
* Select the class in the tree
* Use **View Class Code**
* Copy the code from the Code Window
* Create a PRG file with the class name 
* Paste the code into the new PRG file
* Repeat and for other classes and either add to same file  
  or use one class per file.

Finally if you were using the VCX wwBusiness class and you added the visual class to a FoxPro form visually, you should remove the wwBusiness subclass and instead in your form `Init()` do the following:

```foxpro
THISFORM.AddProperty("oCustomerBus",CREATEOBJECT("cCustomer"))
```

This effectively duplicates the behavior of a dropped visual class and replaces it with a property and the non-visual object.