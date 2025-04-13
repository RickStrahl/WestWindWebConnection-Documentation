An instance of a Message object created with `CreateMessageObject()`.

The structure of this object is:

```js
{
    // a routing action that allows the server or 
    // client to differentiate messages
    action: action,

    // actual message string - can also be JSON
    message: message,

    // GroupId for the current user
    groupId: groupId,
    
    // userId for the sending user
    userId: userId,

    // Recipient list - can be empty which goes to the 'default' empty group
    recipients: [
        { type: 'group', value: 'chatusers' }
        { type: 'allButUser', value: 'rstrahl' }
    ]
}            
```