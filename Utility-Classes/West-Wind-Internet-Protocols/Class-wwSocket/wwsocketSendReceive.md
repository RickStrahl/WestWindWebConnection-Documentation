Sends a string to the open socket, waits for a response and returns the string response. 

You should use SendReceive only on short requests that you know will retrieve typical single line responses as there's no way to know how much data is coming down. Sendreceive() simply combines a single Send() and Receive() method call and returns the result, which is very useful in typical TCP/IP command protocls as SMTP/FTP etc. 

For longer content you should use the WaitFor() (if there's a terminating character) or Receive() methods (if you know the size of the output). Using Receive you'll likely have to loop to keep reading until there's no more data.