.NET System.Guid values cannot be passed to VFP as they are non COM exported value types. So any Guid values passed to and received from .NET need to be passed around as a ComGuid instance. This class wraps an internal Guid instance member and allows access to the GuidString property via string.

**Properties**  
* GuidString *
The string value of the Guid. You can read the value or assign it which in turn updates the internal instance.

**Methods**  
* NewGuid()*
Creates a new new guid value.