TripleDES encryption is the default for encryption and the simplest way to use it is by omitting most of the parameters:

```foxpro
DO wwEncryption

LOCAL loEnc as wwEncryption
loEnc = CREATEOBJECT("wwEncryption")

lcOrig = "Hello World"
? lcOrig

lcKey = "12345678901234567890123456789012"
? lcKey
? LEN(lcKey)

lcVal  = loEnc.EncryptString(lcOrig, lcKey)
? lcVal

lcOrig  = loEnc.DecryptString(lcVal, lcKey)
? lcOrig
```

The defaults use:

* TripleDES Encryption
* ECB Cipher
* MD5 Key Hashing 


This is equivalent to using:

```foxpro
lcVal  = loEnc.EncryptString(lcOrig, lcKey, .F., "TripleDES", "ECB", "MD5")
? lcVal

lcOrig  = loEnc.DecryptString(lcVal, lcKey, .F., "TripleDES", "ECB", "MD5")
? lcOrig
```

You can also use  CBC mode with an IV (8 bytes):

```foxpro
*** CBC DES: 8 bytes
lcIVKey = "12345678"

*** 16 or 24 byte keys for CBC
lcKey2 = PADR(lcKey,24)
? LEN(lcKey2)

*** Use CBC Cipher with IV with TripeDES
lcVal  = loEnc.encryptstring(lcOrig, lcKey2, .F., "TripleDES", "CBC", "", "", lcIvKey)
? lcVal

lcOrig2  = loEnc.decryptstring(lcVal, lcKey2, .F., "TripleDES", "CBC","", "", lcIvKey)
? lcOrig2
```