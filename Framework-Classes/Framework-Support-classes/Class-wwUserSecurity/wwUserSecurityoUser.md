The actual member that holds user data. Filled by the GetUser and NewUser methods.

**Pk**  
C (10)
The unique ID for the user. Shouldn't be manually set usually automatically created by NewUser() with SYS(2015)

**Username**  
C (15)
The User Id for the user used in password validation

**Password**  
C (15)
The password for validating the user.

**Fullname**  
C (40)
The full name.

**Mappedid**  
C (15)

**Email**  
M (4)
The email address for the user.

**Notes**  
M (4)



**Properties**  
M (4)

**Log**  
M (4)

**Level**
N (8,2)

**Admin**  
Determines if this is an admin user that can adminster users.
L (1)

**Created**  
T (8)

**Laston**  
T (8)

**Expireson**  
D (8)

**Logoncount**  
Number of times this user has logged on
I (4)

**Active**  
Determines whether the account is active
L (1)


**Validate**  
A validation string that is set when a new account is created or a password is recovered. This id has to be validated in order to activate the account.
V (40)