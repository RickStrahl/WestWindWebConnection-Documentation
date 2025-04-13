Sets the SaltBytes for the ComputeHash() function globally so when you don't pass the parameter this
value is used. Call this at the start of your application.

Set during application startup which makes the key global for the application. 

**Note:**  
It's recommended that you use **unique salt values** rather than fixed salt values to ensure maximum randomization of your hashes. See the example in the ComputeHash() topic to see how you can manage passwords for example.