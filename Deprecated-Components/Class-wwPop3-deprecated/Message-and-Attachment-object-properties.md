Message objects are retrieved out of the aMessages array. Each element in the array is a message object. Each message can optionally contain array of attachments if ParseMultipartMessage() was called for the message. Each element in the attachment array is an attachment object.

The properties for these objects are as follows:

### Message Object:

**cSubject**  
Subject of the message.

**cBody**  
The content of the message.

**cFromEmail**  
The email address of the sender.

**cFromName**  
The name of the sender. If not provided this may be the email address only.

**cTo**  
The recipient of the message. Full email address and name.

**nSize**  
Size of the message in bytes - rough size that includes the header and attachments.

**cContentType**  
The content type of the message. text/plain, text/html are common. Multipart messages can also be parsed.

**cDate**  
A date string that is supplied if the mail client stamped the message. May be empty in many cases.

**cMultipartBoundary**  
Multipart boundary - internal property used in parsing multipart messages

**cFullText**  
The entire message as retrieved from the mail box. Unparsed including SMTP header.

**nAttachments**  
Number of attachments. Set only when ParseMultipartMessages() is called.

**aAttachments**  
Array of attachments if any. Set only when ParseMultipartMessages() is called.


### Attachment Object:

**cFileName**  
Name of the file attached.

**cBody**  
The binary content of the file.

**nSize**  
Size of the file.

**cContentSize**  
Content type of the file.