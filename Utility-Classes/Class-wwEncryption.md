wwEncryption provides helper functions for two-way string encryption and decryption and one-way string Hash generation.

### Two-way Encryption
Encryption refers to two-way encrypting and decrypting of data. Two-way encryption is useful for storing things like secret configuration values that need to be encrypted for storage, but have to be read back later for consumption. Very common for storing configuration values, where the values are significant and contain data needed by the application for later retrieval and usage. 

This classes Uses **TripleDES** for two-way encryption using internal logic for two-way encoding, which is suitable only for encoding and decoding using this class (or the .NET wrapper).

### Hashing
One-way hashes are useful for storing authorization or validation data like passwords or document verification keys. 

> Hashing is always one way, which means you cannot retrieve the original value from a hashed key. To verify a hash the original value (like a password) has to be provided, which is then re-hashed to compare against a previously stored hash.

The most common use case for hashes is password encryption, where passwords are hashed and stored in a data store. Hashes are created when a password is initially created or generated for a user and that initial hash is saved and stored into a data store. Since the hash is one way the original password is not accessible and therefore can't be easily compromised.  To verify the hash at runtime, a user provides the password (or whatever value) to verify in plain text. That input is then hashed using the same hashing values as the originally stored hash and compared to the hash in the data store for a match.


Hash Algorithms supported:

* MD5
* SHA1
* SHA256
* SHA384
* SHA512
* HMACMD5
* HMACSHA1
* HMACSHA256
* HMAC384
* HMAC512

### CheckSum Creation
There's also support for computing Checksum values from strings and files using MD5 and SHA256 algorithms.l