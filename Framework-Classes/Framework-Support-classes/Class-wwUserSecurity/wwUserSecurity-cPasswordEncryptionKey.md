Encryption key string that's used to hash the password stored in the `usersecurity` file's `password` field. 

The value is hashed when a user is saved via the `SaveUser()`. Hashed passwords end in a postfix of `~~`.