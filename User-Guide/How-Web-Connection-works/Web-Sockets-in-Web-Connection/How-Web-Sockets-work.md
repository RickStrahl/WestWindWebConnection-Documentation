Web Sockets in Web Connection are a **hybrid implementation that mix Web Sockets at the low level Module/Middleware layer and proxy HTTP requests**. The HTTP requests are used to send and receive server side Web Socket messages and forward them to the actual socket so that FoxPro can interact with the sockets. Essentially Web Connection provides an HTTP socket API that makes the socket interaction possible.

> #### @icon-warning Not Raw Web Sockets - Protocol Dependent Implementation
> It's important to understand that **this implementation uses a very specific message protocol** and does not work for capturing or sending of arbitrary socket data. It relies on a **specific message format** into which you can stuff any other data you see fit, but the message wrapper has to exist in order for this implementation to work.
>
> This means you can't send arbitrary messages to this implementation - **it has to be using the specific message format** and you need to have specific code on the client and server to capture and decode these messages.

## Hybrid Implementation
Web Sockets are handled at the server level in the .NET and .NET Core layers. Messages can be sent either via socket requests or specific HTTP requests.

### Client Side from the Browser
An initial Web Socket client - typically from a Web Browser - requests to create a Web Socket via an upgradable HTTP Url on the server and receives back a Web Socket connection using a Web Connection provided JavaScript library (`webconnection-websocket.js`). This uses standard Web Socket connect behavior between the Web browser and the .NET Module/Middleware. In the Web Browser you can use the JavaScript library to receive messages broadcast or you can send messages to be broadcast. 

### Server Side Message Handling via HTTP
Server side Web Connection can't directly interact with the Web Socket created in the Web Connection .NET Module or Middleware handlers because those are external to Web Connection. 

Instead **server bound Web Socket messages are routed to an HTTP URL** (`__WebSocket.ext`), that is handled by your Web Connection server and made available in a standard Web Connection `wwProcess` class via the `wwProcess::OnWebSocket(loSocket)` method that you can override. The socket receives a message object which can contain data and an `action` that determines what task the operation should perform.

FoxPro inside of a Web Connection application or also a Desktop application, can then send a JSON message in  particular format which is handled by the module or middleware, and then fires the Web Socket.

To make this work all Web Socket message use a standard format that looks like this:

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

The same structure is used for sending and receiving.

Essentially the `message` contains the actual data payload. This property can hold plain text which might be all you need, or it can hold a JSON string that can be deserialized and treated as a real data object.

--- 

Next: [Setting up Web Sockets in the Web Browser](VFPS://Topic/_60013JCSM)