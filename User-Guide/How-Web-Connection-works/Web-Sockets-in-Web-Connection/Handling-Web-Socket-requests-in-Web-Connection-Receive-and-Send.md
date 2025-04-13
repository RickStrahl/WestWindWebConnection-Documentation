Server side Web Sockets are handled in Web Connection using a custom `OnWebSocket()` method override in a Web Connection `wwProcess` class. Although Web Sockets are handled inside of the .NET Module or .NET Core Middleware, the raw socket data is intercepted in the module/middleware and resent as a local HTTP request to the Web Connection server using a well-known url `__WebSocket.ext`.

## Configuration
Before we can use the Web Sockets on the server we have to turn on Web Socket process and specify a Web Connection script to which socket requests are routed.

You'll need the following settings:

```xml
   <UseWebSockets>true</UseWebSockets>
   <WebSocketProcessScriptmap>wwd</WebSocketProcessScriptmap>
```   

### Setting the Scriptmap for Web Sockets
Web Socket requests are forwarded to a single Web Connection process class and you need to specify the script map extension in the configuration. For example, in the `wwDemo` app the script map extension is `wwd` which is configured in `web.config` or `webconnection-settings.xml`.

 ```xml
 <WebSocketProcessScriptmap>wwd</WebSocketProcessScriptmap>
 ```
 
Note that only one handler can receive requests so if you have multiple process classes in a single Web Connection server application, only one of them can handle Web Socket requests.

## Setting up the OnWebSocket Handler
Once you have a script map routed to your process class and enabled Web Sockets, any incoming Web socket requests are now routed into Web Connection at `__WebSocket.wwd`. An internal handler picks up the Web Socket request, deserializes the JSON message and passes it to the `OnWebSocket()` handler.

Here's what the `OnWebSocket()` handler looks like for the Chat example:

```foxpro
FUNCTION OnWebSocket
************************************************************************
*  OnWebSocket
****************************************
***  Function: Fired when a Web Socket request comes in.
***    Assume: loMsg.Message, loMsg.UserId, loMsg.GroupId
***      Pass: loMsg             - Incoming (loMsg.Message) from Socket
***            loSocketBroadcast - Use to broadcast message to others
***    Return: nothing
************************************************************************
LPARAMETERS loSocket as wwWebSocketMessage
LOCAL lcMarkdown, loSocketMsg, loMsg

*** This is the Socket payload
loMsg = loSocket.oMessage

*** Use Action to route to different operations
DO CASE

CASE loMsg.action == "broadcastchatmessage"	
	*** Let's modify the incoming message and use it
	*** to broadcast. Inbound and outbound Socket Messages
	*** are identical so it's easiest to just modify original.
	*** Change: action to "broadcast" and message to new/updated value
	* loMsg.action = "broadcastchatmessage"   && we're sending to same action, but you can change it
	
	*** Parse incoming message as Markdown
	lcMarkdown = Markdown(loMsg.message)
	lcMarkdown = ALLTRIM(lcMarkdown) && RTRIM(LTRIM(lcMarkdown,0,"<p>"),"</p>")	
	loMsg.message = lcMarkdown
		
	*** Broadcast the message
	loSocket.BroadcastMessage(loMsg)   && lomsg
	
	*** Alternately create a new message from scratch and send
	* loSend = loSocket.CreateMessageObject()
	* loSend.Action = "broadcastchatmessage"
	* loSend.Message = "<p>New <b>Message</b></p>"
	* loSend.GroupIp = loMsg.Groupid
	* loSend.UserId = loMsg.UserId
	* loSend.AddRecipient("MyGroup","group")
	* loSocket.BroadcastMessage(loSend)   && lomsg
ENDCASE

ENDFUNC
*   OnWebSocket
```

The `OnWebSocket(loSocket)` method gets passed an instance of `wwWebSocketMessage` which provides access to various helpers to manipulate the socket. More importantly this object contains an `oMessage` instance that is a deserialized instance of the Web Socket message shown in previous topics. The message is always the same and includes `action`, `message`, `groupId`, `userId` properties.

In this example the logic is very, very simple. The message to broadcast comes in and all we really need to do is turn the text the user sent - `loMsg.message` - parse the text from Markdown to HTML, and then broadcast the message to all users using `loSocket.BroadcastMessage()`.

The call to `loSocket.BroadcastMessage(loMsg)` sends the message through a Web Socket to all connected clients.

> #### @icon-info-circle Broadcasting to a Web Socket from FoxPro? 
> So how does this work? Your Web Connection code has no access to the socket, so how does the message get sent? 
>
> Web Connection uses a hack to allow interaction with the Web Socket via an HTTP request to `__broadcastWebSocket.wc` and passing a standard Web Socket message object. A special handler in the .NET Module/Middleware picks up this request and feeds it to the connected Web sockets that are maintained in memory. 
>
> Voila: You can broadcast Web Socket messages  from FoxPro code. This works in Web Connection but also works from a standalone application that can load the `wwWebSocketMessage.prg` class.


## Calling a Web Socket outside of Web Connection
You can also use this same exact broadcasting code outside of Web Connection. Since `__broadcastwebsocket.wc` is an HTTP URL you can directly communicate with it using either the `wwWebSocketMessage.prg` class or even using plain `wwHttp` requests.

You can run the following from the FoxPro Command Window or a test program:

```foxpro
DO wwWebSocketMessage
loSocket = CREATEOBJECT("wwWebSocketMessage")
loSocket.cBaseUrl = "http://localhost:5200/"

loMsg = loSocket.GetMessageObject()
loMsg.action = "broadcastchatmessage"  
loMsg.userId = "RickFox"
loMsg.groupId = "Web Connection Chat 03-10-2021"
loMsg.Message = "Hello from FoxPro " + TIME()

loSocket.BroadcastMessage(loMsg)
```

This fires the same exact code and has the same behavior as the code we were running inside of Web Connection. 
  
Note that you have to specify a base server URL for the Web Connection application for this to work, but otherwise - identical to the server code.

Cool right?

> #### @icon-info-circle wwWebSocketMessage is not a Generic Web Socket Library
> The `wwWebSocketMessage` class **is not a generic Web Socket client library**. It's very specific to Web Connection's hybrid Web Socket/HTTP implementation that depends on specific URLs on the server and a very specific message format.