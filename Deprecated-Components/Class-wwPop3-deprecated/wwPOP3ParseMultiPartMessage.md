Parses the cBody property of a message and creates an array of attachments. 

This method needs to be explicitly called if you retrieving messages manually using GetMessage().  GetMessages() by default calls this method unless you pass the llNoParseAttachments is set to .T.