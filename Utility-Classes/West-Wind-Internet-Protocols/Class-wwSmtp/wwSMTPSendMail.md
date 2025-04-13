This method allows sending of SMTP emails which is the most common Email interface for the Internet. This method requires that you have access to an SMTP mail server.

This method sends a message in one step by connecting to the server, sending the message and disconnection. To use it set the properties of the wwSmtp object.

To send email set various property values and send the actual message. The following are a list of the most common properties that need to be set to send a message:

**cMailServer**  
The IP Address or domain name of the SMTP server that is responsible for sending the message. The server must support 'pure' SMTP mail services and must not require a login prior to sending messages.

**cSendername**  
Display name of the person sending the message. This option is often overridden by the mail server with the actual user account, but some servers leave the name as set here.

**cSenderEmail**  
The sender's Email address.

**cRecipient**  
Either a single recipient email address or a list of comma delimited addresses. Limited to 1024 characters. To specify a name and email address use the following syntax (works for all Recipient and CC properties):
*Joe Doe <jdoe@yourserver.com>*

**cCCList**  
Either a single CC recipient email address of a list of comma delimited addresses. Limited to 1024  characters.

**cBCCList**  
Blind CCs are sent messages but do not show on the recipient or CC listing of the receiver.  Limited to 1024 characters.

**cSubject**  
The message subject or title.

**cMessage**  
The actual message body text. 

**cAttachment**  
The name of the file that is to be attached to the message. This property must be filled with a valid path pointing at the file to attach. Multiple files can be attached by separating each file with a comma.

**cContentType**  
Content type of the message allows you to send HTML messages for example. For HTML set the content type to **text/html**. Content types only work with text content types at this time since special encoding of the message text is not supported. All binary types should be sent as attachments.

**cUsername, cPassword**  
If you are sending mail to a mail server that requires username and password authentication, set these properties. Only unencrypted, plain text communication is supported using the AUTH LOGIN command.