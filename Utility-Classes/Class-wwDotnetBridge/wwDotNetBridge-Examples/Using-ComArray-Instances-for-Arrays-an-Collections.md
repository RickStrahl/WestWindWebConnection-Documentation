Array handling through .NET requires special handling as FoxPro arrays do not marshal well over COM Interop boundaries. To make common array and collection easier to use, wwDotnetBridge provides a [ComArray](vfps://Topic/Class%20ComArray) wrapper class that acts as a proxy around a .NET Enumerable (array or collection).

When using `InvokeMethod` or `GetProperty` and returning arrays from the method call,  wwDotNetBridge automatically fixes up any returned arrays or collections as a `ComArray`.

You can also create `ComArray` instances directly and through it manipulate .NET arrays using FoxPro code easily. You can add, edit and remove elements of this one dimensional array and then pass the ComArray as an array parameter using the indirect access methods of `InvokeMethod()` and `SetProperty()` to pass or assign values instead of directly calling the method or setting a property.

### What is ComArray and how does it work?
ComArray works by keeping arrays entirely inside of .NET, never passing the array type to FoxPro, so no array conversion occurs. Rather methods are used to set, retrieve and manipulate individual array elements which provides full access to elements without having to pass the array into FoxPro. This is more efficient, but most importantly it makes it possible for FoxPro to manipulate .NET arrays and collections easily and consistently which is not possible with plain FoxPro arrays. 

Here are a few examples that demonstrate typical usage scenarios:

### Retrieve an Array Result
Array and Collection results retrieved with GetProperty/InvokeMethod() are automatically turned into [ComArray](vfps://Topic/Class%20ComArray) instances which make it easy to manipulate a .NET array.

```foxpro
loBridge = CREATEOBJECT("wwDotNetBridge")

loService = loBridge.CreateInstance("WebStoreService.WebStoreConsumerService")

*** ComArray instance returned - method returns an Array of WebStoreService.wws_item
loArray = loBridge.InvokeMethod(loService,"GetItemArray","Web Development Tools")

*** Loop through the array - array is 0 based
FOR lnX = 0 to loArray.Count -1
   loItem = loArray.Item(lnX)
     
     ? loItem.Sku
     ? loItem.Descript
ENDFOR
```

### Manipulate the Array and pass back to .NET
ComArray instances can then also be passed back to .NET methods and property values when using InvokeMethod/SetProperty respectively. Using the code above loArray is a ComArray instance and you can update with code like the following:

```foxpro
loBridge = CREATEOBJECT("wwDotNetBridge")

loService = loBridge.CreateInstance("WebStoreService.WebStoreConsumerService")

*** ComArray instance returned
loArray = loBridge.InvokeMethod(loService,"GetItemArray","Web Development Tools")


*** Create a new object typed to the Array's element base type (WebStoreService.wws_item here)
loiIem = loArray.CreateItem()  

*** Last statement is the same as this
*loItem = loBridge.CreateInstance("WebStoreService.wws_Item")

loItem.Sku = "NewSKU"
loItem.Descript = "New item"

*** Add a new item to the existing array
loArray.AddItem(loItem)

*** Method that expects an Item array - we're passing our ComArray instance
loBridge.InvokeMethod(loService,"UpdateItemList",loArray)
```

Note that you have to use InvokeMethod() rather than a direct method call to pass the array back to .NET. This ensures that the array never passes into Visual FoxPro.

You can also remove an item:

```foxpro
loArray.RemoveItem(0)  && 0 based index
```

or you can clear the entire array of items:

```foxpro
loArray.Clear()
```

**Assigning a Property**  
Along the same lines you can take a ComArray and assign it to a property. There are two options to do this.

```foxpro
loItem = loBridge.CreateInstance("WebStoreService.wws_Item")
loItem.Sku = "NewSKU"
loItem.Descript = "New item"

loArray.AddItem(loItem)

*** Assign the array to the loItem.Items property
loArray.AssignTo(loService,"Items")
```

or using wwDotnetBridge directly:

```foxpro
*** Assign the loArray to the Items array property
loBridge.SetProperty(loService,"Items",loArray)
```

### Create a new Array and pass to .NET
You can also create a new ComArray instance directly rather than receiving one back form from a .NET method or property as in the last examples. To do this you create a ComArray instance using wwDotnetBridge.CreateArray() method:

```foxpro
*** Create the ComArray instance and specify the item type's full name
loArray = loBridge.CreateArray("WebStoreService.wws_Item")

*** Now add items
*loItem = loBridge.CreateInstance("WebStoreService.wws_Item")
loItem = loItem.CreateItem()   && Creates a WebStoreService.wws_Item instance
loItem.Sku = "NewSKU"
loItem.Descript = "New item"
loArray.AddItem(loItem)

loItem = loItem.CreateItem()  
loItem.Sku = "NewSKU2"
loItem.Descript = "New item 2"
loArray.AddItem(loItem)

*** Method that expects an Item array - we're passing our ComArray instance
loBridge.InvokeMethod(loService,"UpdateItemList",loArray)
```

Once the array exists you can then again use `InvokeMethod()` to pass it as a parameter of a method or use `ComArray.AssignTo()` or `SetProperty()` to set a property. As before you can't pass or set the ComArray directly on the .NET object instance - you need to use the indirect methods.