The wwWebSocket class provides basic helpers to Web Connection's Web Socket implementation. 

This class can:

* Create an instance of Web Socket Message object
* Ability to Send a Web Socket Message

This class can be used in two ways:

* As incoming Message object in `wwProcess::OnWebSocket(loSocket)`
* Standalone for sending a message outside of Web Connection

### OnWebSocket Usage

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

*** This is the Socket Payload object
loMsg = loSocket.oMessage

DO CASE
CASE loMsg.action == "broadcastchatmessage"
	loSocket.AddRecipient(loMsg.GroupId, "group")
	
	*** Parse incoming message as Markdown
	lcMarkdown = Markdown(loMsg.message)
	lcMarkdown = ALLTRIM(lcMarkdown) && RTRIM(LTRIM(lcMarkdown,0,"<p>"),"</p>")

    *** Change incoming message with updated HTML
	loMsg.message = lcMarkdown
	
	*** Broadcast the message
	loSocket.BroadcastMessage(loMsg)
ENDCASE

ENDFUNC
*   OnWebSocket
```

### Standalone Usage

```foxpro
DO wwWebSockets
loSocket = CREATEOBJECT("wwWebSockets")
loSocket.cBaseUrl = "http://localhost:5200/"
loMsg = loSocket.GetMessageObject()

loMsg.userId = "RickFox"
loMsg.Message = "Hello from FoxPro " + TIME()
loMsg.action = "broadcastchatmessage"  && send to everybody
loSocket.BroadcastMessage(loMsg)
```