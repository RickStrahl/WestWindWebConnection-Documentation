AES encryption 

* Requires a 16 or 24 byte key
* Often uses `CBC` Cipher Mode which requires a 16 byte IvKey


```foxpro
Do wwEncryption

LOCAL loEnc as wwEncryption
loEnc = CREATEOBJECT("wwEncryption")

*** Original Text to convert
lcOrig = "Hello World"
? lcOrig

*** Raw key (no hashing) of 24 (or 16) bytes
lcKey = "123456789012345678901234"
? lcKey
? LEN(lcKey)

*** IV Bytes - 16 bytes
lcIvKey = "1234567890123456"

*** No hashing
lcVal  = loEnc.EncryptString(lcOrig, lcKey, .F., "AES", "CBC","","",lcIvKey)
? lcVal

lcOrig  = loEnc.EncryptString(lcVal, lcKey, .F., "AES", "CBC","","",lcIvKey)
? lcOrig
```

Note you can use other Cipher modes but make sure that you provide the proper size keys.