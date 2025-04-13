Allows you to specify extra HTTP or SMTP headers when sending requests to Web Servers or SMTP serveres. 

Any extra headers provided should be in the following key:value format:

**Content-type: text/xml**  

and include a CRLF at the end of the line.

For SMTP users certain values may not be overridden reliably - all the values set by wwIPStuff.dll when sending email can be set but may or may not actually be used due to duplication. To see the headers that wwIPStuff sets send yourself an email and look at the headers with an email client like Outlook that shows you headers optionally.