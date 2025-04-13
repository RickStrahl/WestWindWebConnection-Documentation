On the client side you can interact with Web Connection WebSockets via the `\scripts\webconnection-websocket.js` library which provides the core features for handling socket events and sending messages to and from a Web Socket.

## What do you need

* Web Sockets require an evergreen browser (no IE support)
* Uses modern ES2016 JavaScript syntax
* Requires Web Server that supports WebSockets  
<small>*(IIS 10, IIS Express 10, Server 2016+, Web Connection Web Server)*</small>

## Setting up a Web Socket
The following is from the Web Socket Chat Sample application.

```js
import {WebConnectionWebSocket} from '../scripts/web-connection-websocket.js'

// Create the Socket instance
var socket = new WebConnectionWebSocket();

// handle message object { action, message, groupId, userId } props
socket.onMessageHandler = (msg) => {
    // just for reference
    var action = message.action  // for routing
    var message = msg.message;   // string message
    var user = msg.userId;       // User that sent it
    var group = msg.groupId;     // Group that sent it
    
    // typically you can route on action
    if (action == "broadcastchatmessage") {
        var msg = message;   // plain text message
    }
    else if(action == "initialmessages") {
        var msgs = JSON.parse(message);   // JSON message:  array of messages
    }
    // recommend you 'route' any actions to separate methods to keep
    // this function from getting huge
}  
// events when sockets are connected and closed
socket.onOpenHandler =(ev) => {    // optional 
}
socket.onCloseHandler = (ev) => {  // optional 
}

var group = $("#group").val(); // from input field
var user = $("#user").val();   // from input field

// this actually creates the socket 
// pass group/user if you need to differentiate recipients
// if you broadcast to all this is not needed
socket.tryConnect(true, groupId, userId);
```

The crux of this code is the `socket.onMessageHandler` which receives incoming Web Socket messages. The `msg` parameter is a Web Connection message object which looks like this:

```js
{
    // a routing action that allows the server or 
    // client to differentiate messages
    action: action,

    // actual message string of what the Socket sent - can also be JSON
    message: message,

    // GroupId of sender (if available)
    groupId: groupId,
    
    // userId of sender (if available)
    userId: userId, 
    
    recipients: []
}            
```

Based on an incoming message you typically use the `action` to determine how to handle the incoming message and `message` contains the actual message payload. The `message` can be plain text like in the Chat application, or it can be complex object serialized as JSON in which case you need to deserialize it with `JSON.parse(msg.message)`.

### Message Handling Example
This code basically provides the ability to handle incoming Web Socket requests. In the Chat example this corresponds to capturing a message that is broadcast to all users and displaying the message:

```js
socket.onMessageHandler = (msg) => {        
    userId = userName$.val();
    
    if (msg.action == "broadcastchatmessage") {
        if (!msg && !msg.message)            
            return;  // empty - do nothing

        // grab the raw HTML of existing message
        var val = msgs$.html();

        var html = "<div class='message-wrapper'>";
        
        // if current user add special class to highlight
        if (userId == msg.userId)
            html = "<div class='message-wrapper this-user'>";

        // display the message header with the user Id of sender            
        html += "<p class='message-header'>--- <b>" + msg.userId + "</b>  <small>" + new Date().toLocaleTimeString().toLowerCase() + "</small> ---</p>";               
        
        // msg.message is just plain text
        html += msg.message;        
        
        html += "</div>";

        // and assign to Message area
        msgs$.html(val + html);

        // scroll to bottom
        setTimeout(() => {
            msgs$[0].scrollTop = 9999999999 
            highlightCode();   // highlight markdown code snippets
        }, 120);
    }
}  
```

## Sending Messages from the Client
Clients also need to 'broadcast' messages to a Web Socket. In the chat application a user can type a message, which is then sent via the Web Socket to the Web Connection server, which then broadcasts the message to connected clients.

On the client side the `WebConnectionSocket` class also supports sending messages to the server, which looks like this:

```js
btnSend$.on("click",function(e) {
    var userName = userName$.val();
    var group = group$.val();    
    var message = send$.val();

    // create a broadcast message object - with action and message
    var msg = socket.createBroadcastMessage("broadcastchatmessage", message);
    msg.userId = userName;
    msg.groupId = group;
        
    // specified group(s) to send to - if empty (goes to 'empty' group)
    if (group)
        msg.addRecipient(group, 'group')
            
    socket.send(msg);
    send$.val('');
    send$.focus();    
});
```

And that pretty much handles the send operation. The key is `socket.send(msg)` where the `msg` is in the standard format shown above. You should use `socket.createBroadcastMessage()` which creates an object with the requisite properties and an `addRecipient()` method to filter the recipient list to a specific group or user(s).

---

Next: [Server Side Web Socket Processing](VFPS://Topic/_60013JYCN)