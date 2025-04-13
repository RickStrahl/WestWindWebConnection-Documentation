This is the low level string that is used to hold any RAW headers you want to add to a request. This is an override to Headers.Add() to allow for any headers or content that doesn't follow the name/value syntax of headers. 

Each header should be followed by a CRLF!

Web Connection build up headers by looping through all of the headers in the Headers collection and then simply appends this raw string to the header.