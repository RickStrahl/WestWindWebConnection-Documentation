Web Connection supports several logging mechanisms including the Web Connection Server log and starting with version 5.0 and optional ISAPI logging mechanism. The ISAPI Log is a new feature that can be used for debugging requests and mapping ISAPI requests to requests running through your FoxPro server. 

The ISAPI request creates a unique RequestId at the beginning of the request which consists of an ISAPI threadID and a GUID. This value is passed through the entire request and to your FoxPro server application where it becomes available as Request.ServerVariables("REQUESTID"). The FoxPro Server also picks up this value and logs it in the request log if logging is enabled.

The ISAPI extension can also log every request into it by setting the wc.ini *LogDetail* key to 1. If set to 1 every request is logged when it starts and when it exits the DLL. Here's a sample of what this looks like:

2006.02.6 02:50:02:359 - 2428_D8152FCDDCE5 - Request Started - /wconnect/wc.dll?_maintain~ReadSetupIni - 122
2006.02.6 02:50:02:359 - 2428_D8152FCDDCE5 - Request Completed (0) - /wconnect/wc.dll?_maintain~ReadSetupIni - 3
2006.02.6 03:12:47:421 - 2428_5B5EC48F1980 - Request Started - /wconnect/wc.dll?wwDemo~ShowImage - 122
2006.02.6 03:12:47:531 - 2428_5B5EC48F1980 - Request Completed (109) - /wconnect/wc.dll?wwDemo~ShowImage - 3
2006.02.6 03:12:58:218 - 4188_ABAD0B86AE31 - Request Started - /wconnect/weblog/default.blog? - 122
2006.02.6 03:12:59:750 - 4188_ABAD0B86AE31 - Request Completed (1531) - /wconnect/weblog/default.blog? - 3
2006.02.6 03:13:06:671 - 4188_96F7C8213A96 - Request Started - /wconnect/weblog/default.blog? - 122
2006.02.6 03:13:07:437 - 4188_96F7C8213A96 - Request Completed (765) - /wconnect/weblog/default.blog? - 3
2006.02.6 03:13:08:734 - 4188_14A1C723EAC7 - Request Started - /wconnect/weblog/admin/default.blog? - 122
2006.02.6 03:13:09:171 - 4188_14A1C723EAC7 - Request Completed (438) - /wconnect/weblog/admin/default.blog? - 3

Each entry logs a timestamp, the request Id and a message - in this case start and stop messages. Stop messages include a tick count in parenthesis which is the time it took to process the request. 

End messages can sometimes not get logged if a hard exception occurs. In that case you should see a different kind of message for a specific id logged instead.