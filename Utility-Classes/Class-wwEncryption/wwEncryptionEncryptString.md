Symmetric two-way encryption with using string key with **TripleDES** or **AES** encryption. There are number of configuration options for provider, cipher, IV and optional hash value.

> Although many combinations are available on this complex method, for simple two-way encryption using this library on both ends, you can just use the defaults with the first two or three parameters.

```foxpro
lcOrig = "Hello World"
lcKey = "f$addawd/\333#adawuy@!ds"

*** Example using Default settings for TripleDES 
lcVal  = loEnc.EncryptString(lcOrig, lcKey)
? lcVal  && encrypted

lcOrig2  = loEnc.DecryptString(lcVal, lcKey)
? lcOrig2  && Hello World
```

This default usage uses:

* TripleDES
* ECB Cipher Mode
* PaddingMode PKCS7 (can't be changed currently)
* MD5 Key Hashing (to 16 bytes)
* base64 Encoded binary result string

You can customize the Provider (TripleDES and AES), CipherMode. 

You can also optionally provide a key hash and salt. Use the latter only if you're using this library on both ends, or if an external library requires it. It's not uncommon to see libraries that require MD5 or SHA256 pre-hashed keys.

Encryption implies two-way that uses `EncryptString()` and `DecryptString()` to encode and decode strings. The output from encryption is a binary string in **base64** (default) or **binHex** format.

It's important that `DecryptString()` uses the same parameters as `EncryptString()` to ensure that encrypted values can be round tripped!

{{ Helpers.ChildTopicsList() }}