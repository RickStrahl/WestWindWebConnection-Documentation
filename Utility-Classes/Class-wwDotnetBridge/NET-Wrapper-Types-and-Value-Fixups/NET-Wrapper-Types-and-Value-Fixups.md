There are a number of types that simply don't work or work very badly inside of Visual FoxPro. For example, the .NET System.Guid type cannot be returned into Visual FoxPro and accessed directly - it simply crashes when accessed in any way in FoxPro. Arrays are another problem type because FoxPro's limited support for array management functions that don't map directly to COM's referenced based arrays.

As a workaround wwDotNetBridge provides a few wrapper types that can act as a bridge to accessing these types inside of Visual FoxPro. These types are available on calls to InvokeMethod, SetProperty and GetProperty which automatically translate inbound parameters and result values to these specialty types. They are NOT available on direct calls on .NET object instances.


> These features are only available if you use the 'implicit' functions of wwDotNetBridge like InvokeMethod, GetProperty, SetProperty. If you call methods or get/set properties directly on the return .NET object instances you will not get any of this translation.
>
> For example, if you call a method that takes a Guid parameter, pass a ComGuid instance instead and wwDotNetBridge will automatically convert the ComGuid into a .NET Guid structure. If a function or value returns a ComGuid your FoxPro code automatically receives the ComGuid wrapper type.

The following wrapper classes are available:

<%=  ChildTopicsTableHtml(oHelp,"CLASSHEADER","Wrapper Class","oHelp.FormatHtml(oHelp.oTopic.Body)")%>