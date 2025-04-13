This is a marker property that lets any calling code check if the callback result is an Exception. 

```javascript
function OnError(Result)
{
    if (Result.isCallbackError)
    {
        alert(Result.message);
        return;
    }

	// do what you need to with Result
	alert(Result);
}
```