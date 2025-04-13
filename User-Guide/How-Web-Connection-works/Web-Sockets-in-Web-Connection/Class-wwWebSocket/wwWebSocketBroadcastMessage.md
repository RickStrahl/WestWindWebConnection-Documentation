This method allows you to broadcast a message to connected Web Socket clients at the server you are targeting.

The `loMsg` object can either be:

* An incoming `loSocket.oMessage` object in `wwProcess::OnWebSocket(loSocket)`
* A standalone message for sending in  desktop application

In either scenario you can customize the `loMsg` instance as needed

Example:

```foxpro
DO wwWebSockets
loSocket = CREATEOBJECT("wwWebSockets")
loSocket.cBaseUrl = "http://localhost:5200/"
loMsg = loSocket.CreateMessageObject()

loMsg.userId = "RickFox"
loMsg.message = "Hello from FoxPro " + TIME()
loMsg.action = "broadcastchatmessage"  && send to everybody
loSocket.BroadcastMessage(loMsg)
```