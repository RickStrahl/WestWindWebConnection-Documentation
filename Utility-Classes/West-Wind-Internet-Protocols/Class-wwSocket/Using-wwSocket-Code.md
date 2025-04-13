Here's a simple example for accessing a Web Page and retrieving the content using wwSocket. Not a super practical example - use wwHttp, but it demonstrates simple use cases for send/receive operations:

```foxpro
* ** Basic send Email to an SMTP server with sockets
#INCLUDE wconnect.h
DO WCONNECT

DO wwSocket
LOCAL loIP as wwSocket
loIP = CREATE( "wwSocket")
loIP.lStripNulls = .T.
loIp.nTimeout = 5
loIp.LLOGSESSION = .t.
CLEAR

TEXT TO lcHttp NOSHOW
GET http://www.west-wind.com/ HTTP/1.1
Host: www.west-wind.com
Connection: keep-alive
Cache-Control: max-age=0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
User-Agent: Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/537.31 (KHTML, like Gecko) Chrome/26.0.1410.43 Safari/537.31
Accept-Encoding: gzip,deflate,sdch
Accept-Language: en-US,en;q=0.8
Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.3
ENDTEXT

lcHttp = lcHttp + CRLF + CRLF

? loIP.Connect("west-wind.com",80)
? loIP.Send(lcHttp)

lcText = "!"
DO WHILE !IsNullOrEmpty(lcText)
	lcText = loIP.Receive(2000) 
	? lcText

	IF LEN(lcText) < 2000 AND ATC("</html>",lcText)	> 0
	   EXIT
	ENDIF
ENDDO

? loIp.Disconnect()
```

The following is an example of accessing an SMTP server and sending a message:

```foxpro
* ** Basic send Email to an SMTP server with sockets
#INCLUDE wconnect.h
DO wwSocket

loIP = CREATE( "wwSocket")
loIP.lStripNulls = .T.

CLEAR

? loIP.Connect("mail.somemailserver.net",25)

? loIP.Receive() && Welcome Message
? loIP.Send("helo Rick" + CRLF)
? loIP.Receive()
? loIP.Send("mail from: senderemail@yourserver.com" + CRLF)
? loIP.Receive()

? loIP.Send("rcpt to: <senderemail@yourserver.com>" + CRLF)
? loIP.Receive()

? loIP.Send("DATA" + CRLF)
? loIP.Receive()

TEXT TO lcMailBody NOSHOW
To: recipient@someserver.com
From: senderemail@yourserver.com (Rick Strahl)
Subject: Test Message

Whass-up, doc?
.
ENDTEXT

? loIP.Send(lcMailBody + CRLF)
? loIP.Receive()

? loIP.Send("quit" + CRLF)
? loIP.Receive()

? loIP.Disconnect()
```