Use this method to switch the **global setting** for output mode of binary string values  for `ComputeHash()` and `EncryptString()` to either **base64** (default) or **binHex**.

This method changes a **global setting** that is sticky, until you change it again.

> Note: `ComputeHash()` has recently changed to explicitly include a `llUseBinHex` parameter that overrides this setting when passed.