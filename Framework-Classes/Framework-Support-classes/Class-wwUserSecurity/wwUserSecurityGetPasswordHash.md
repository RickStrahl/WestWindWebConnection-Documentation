Retrieves a password hash from an unenrypted string/password value. 

This method is used internally to encrypt passwords during Save operations. By default this method looks for a ~~ postfix and if it finds it uses that value as a hash. The `llForce` parameter forces the password to be rehashed explicitly.