The high level GetMessages() method retrieves all messages from a mailbox into a local object which you can parse easily with code like the following:

<pre>#INCLUDE wconnect.h
do wwPOP3  && Load Libraries

loPOP = CREATEOBJECT("wwPOP3")
loPop.cMailServer = "mailserver.net"
loPop.cUserName = "rickstrahl"
loPop.cPassword = "password"

* ** Create a log string
loPop.lLogSession = .T.

IF !loPOP.Connect()
   ? "Couldn't connect: " + loPOp.cErrorMsg
   RETURN
ENDIF

lnMsgs = loPop.Getmessages()
? "Messages in Inbox: " + TRANSFORM( lnMsgs,"9,999" )
? "---"
For x = 1 to lnMsgs
   ? loPop.aMessages[x].cSubject
   ? "From: " + loPop.aMessages[x].cFromName + "  Size: " + ;
TRANSFORM(loPop.aMessages[x].nSize/1000,"999,999,999.99") + "kb"
   ? loPop.aMessages[x].cBody
   IF (loPop.aMessages[x].nAttachments) > 0
      ? "Attachments: "
      FOR y = 1 TO loPop.aMessages[x].nAttachments
         ?? loPop.aMessages[x].aAttachments[y].cFileName + " "
         * lcAttachment = loPop.aMessages[x].aAttachments[y].cBody
      ENDFOR
      ? "---"
   ENDIF
endfor

RETURN
</pre>

Note that the above code does not delete messages, which can be accomplished by passing the second parameter to GetMessages() as .T.

You can also use the low level methods to retrieve messages one at a time and parse them as needed or just retrieve headers etc.