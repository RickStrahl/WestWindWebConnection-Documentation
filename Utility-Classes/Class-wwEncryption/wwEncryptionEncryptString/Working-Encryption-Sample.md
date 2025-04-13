The following is a full self-running sample that you can cut and paste into a `PRG` file to demonstrate the basic features of the `EncryptString()` and `DecryptString()` methods  for synmmetrical encryption:

```foxpro
CLEAR ALL
DO wwEncryption

CLEAR

LOCAL loEnc as wwEncryption
loEnc = CREATEOBJECT("wwEncryption")

lcOrig = "Hello World"
? lcOrig

lcKey = "12345678901234567890123456789012"
? lcKey
? LEN(lcKey)

? "*** Hashing ***"

loEnc.SetBinHexMode(.T.)
lcVal = loEnc.ComputeHash(lcOrig, "HMACSHA256", lcKey,.T.)
? lcVal
?

? "*** Encryption: Defaults ***"

lcVal  = loEnc.encryptstring(lcOrig, lcKey)
? lcVal

lcOrig2 = loEnc.decryptstring(lcVal, lcKey)
? lcOrig2

?

? "*** Encryption: TripleDES ***"

*** DES with CBC: 8 bytes IV
lcIVKey = "12345678"


*** 16 or 24 byte keys for CBC
lcKey2 = PADR(lcKey,24)
? LEN(lcKey2)

*** Use CBC Cipher with IV with TripeDES
lcVal  = loEnc.encryptstring(lcOrig, lcKey2, .F., "TripleDES", "CBC", "", "", lcIvKey)
? lcVal

lcOrig2  = loEnc.decryptstring(lcVal, lcKey2, .F., "TripleDES", "CBC","", "", lcIvKey)
? lcOrig2

?

? "*** Encryption: AES ***"

*** AES CBC: 16 bytes IV
lcIVKey = "1234567890123456"


*** 16 or 24 byte keys for CBC
lcKey2 = PADR(lcKey2,24)
? LEN(lcKey2)

lcVal  = loEnc.encryptstring(lcOrig, lcKey2, .F., "AES", "CBC","","",lcIvKey)
? lcVal

lcOrig2  = loEnc.decryptstring(lcVal, lcKey2, .F., "AES", "CBC","","",lcIvKey)
? lcOrig2
```

### Key and IV Sizes
Figuring out the right sizes for key and IV bytes is complicated, and depends on the specs of your application, and specs of the algorithm used. Please check with your provider to find out the exact sizes when working with third party integrations.