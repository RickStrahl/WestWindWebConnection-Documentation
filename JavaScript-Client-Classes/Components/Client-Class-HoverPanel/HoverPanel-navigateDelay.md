Determines the time in milliseconds before a call to startCallback actually triggers the callback on the server.

The delay is used to check if the busy status has changed for example by navigating of the a URL or other hover operation. In general for any hover operations it's recommended you use a 300-500 millisecond delay to avoid excessive navigation upon the slightest access of a hover link.