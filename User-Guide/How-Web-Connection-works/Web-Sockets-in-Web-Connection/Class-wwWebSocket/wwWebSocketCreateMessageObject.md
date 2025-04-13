Creates a Web Socket message object the structure that is used to broadcast messages to all connected recipients.
  
By default this method sets the `oMessage` instance as well as returning the created message object.

Message object structure:

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

Recipients is a collection and should be added using the `AddRecipients(lcUserOrGroup, lcType)` method. You can clear the collection with `loMsg.Recipients.Clear()`