Collection and arrays require special attention in .NET and depending on the type of collection and whether generic collections are used or not, you can take advantage of the wwDotNetBridge objects.

Collections tend to be accessed by indexers, yet FoxPro can't access collections this way. Most of the list collection types don't have item retrieval methods beyond the indexer and in those scenarios wwDotNetBridge can use Reflection to get at the data. Also any collection that is based on a generic type anywhere in its hierachy (like List<Person> for example) will not be accessible through COM directly. Again Reflection and wwDotNetBridge does allow access to these generic types.

Assume a .NET Generic type definition of a List<T> collection property like this:

```csharp
public List<Person> Persons
{
    get { return _Persons; }
    set { _Persons = value; }
}
private List<Person> _Persons = new List<Person>();
```

Because this type is generic it can't be accessed in VFP. You can use wwDotNetBridge to access it with the following code:

```foxpro
loBridge = CREATEOBJECT("wwDotNetBridge")
loItem = loBridge.CreateInstance("Westwind.WebConnection.VfpTestClass")

loPerson = loBridge.CreateInstance("Westwind.WebConnection.Person")
loPerson.Name = "Jumbo"

*loItem.Persons.Add(loPerson) && doesn't work
loBridge.InvokeMethod(loItem.Persons,"Add",loPerson)

*loItem = loItem.Persons[0]  && doesn't work
loPerson = loBridge.GetPropertyEx(loItem,"Persons[0]")
? loPerson.Name

* loItem.Person[0] = loPerson && doesn't work
loBridge.SetPropertyEx(loItem,"Persons[0]",loPerson)

*** add another record
loBridge.InvokeMethod(loItem.Persons,"Add",loPerson)
? loBridge.GetProperty(loItem.Persons,"Count")  && 2

loBridge.InvokeMethod(loItem.Persons,"Clear")
? loBridge.GetProperty(loItem.Persons,"Count")  && 0
RETURN
```

Please note that calling the Add and Clear method may be possible DIRECTLY with some collection types depending on the specific collection type used. For example, if you use classic (.NET 1.x) collection types all of the properties and methods except the indexers should be directly accessible. When working with code, you should try direct access first, then fall back on these Reflection methods if they don't work.