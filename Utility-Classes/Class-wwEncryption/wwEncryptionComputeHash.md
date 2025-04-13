Hashes are one-way encrypted values that can be used to store passwords and other secure data that needs to be verified, but does not need to be retrieved in its original format. 

> Hash output is returned as a **base64** (default) or **binHex** string. Make sure you are using the correct format to match your requirements! Many third party services need you to provide hashes in **binHex** format.

Passwords can be hashed and you can validate a password by rehashing the data from which it is created. Whenever you can encrypt data in a completely repeatable way to verify it, a one-way hash is preferable over two-way encryption.

> Hashes can never retrieve the original value from which the hash was created. You can only validate a hash by recreating it with the same input data.

The `ComputeHash()` method creates a one-way hash of an input string useful for passwords or authorization tokens using any of the following hashing algorithms:

* MD5
* SHA1
* SHA256
* SHA384
* SHA512
* HMACMD5
* HMACSHA1
* HMACSHA256
* HMACSHA384
* HMACSHA512

> The HMAC Versions **require** that you pass a hash salt value. For the others it is optional.

You can provide an optional salt to further randomize the hash. It's recommended you use a **unique Salt Value** for each hash you create, such as a user id when password hashing for example.