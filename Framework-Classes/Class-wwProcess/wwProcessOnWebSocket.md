Allows you to interact with Web Connection Web Sockets using a custom hybrid message protocol. This method lets you handle Web Socket messages from clients by accessing the `loSocket.oMessage` parameter, updating the message and broadcasting a new message to connected Web Sockets optionally.

For more info please see:

* [Web Sockets in Web Connection](VFPS://Topic/_600118EDC)

Example for Socket Handling:

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
	* loSend.Message = "..."
	* loSend.GroupIp = loMsg.Groupid
	* loSend.UserId = loMsg.UserId
	* loSend.AddRecipient("MyGroup","group")	
    * loSocket.BroadcastMessage(loSend)   && lomsg
ENDCASE

ENDFUNC
*   OnWebSocket
```