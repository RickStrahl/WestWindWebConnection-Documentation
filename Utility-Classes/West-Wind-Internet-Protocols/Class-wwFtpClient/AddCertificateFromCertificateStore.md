Adds a certificate from the Windows Certificate store. You can add from the Current User Personal store or from the Local Machine Store.

To find the certificate you can specify a `lcSubject` parameter which references the Certificate's subject string. You can either provide the full Subject string or a partial value. Make sure the value is unique so it matches a single certificate.

Certificate subjects look like this:

```
CN=example.com, O=Example Corp, OU=IT Department, L=Seattle, S=Washington, C=U
```

Certificates always have a `CN` value, but others may be missing for certain types of certificates like non-public, manually shared client certificates.

You can find the subject in the Certificate by looking up the certificate in the Certificate store and going to **Details -> Subject**.

You can use either a full subject name or a partial values to identify the subject. A partial value for the example above might be `CN=expample.com`. Just make sure the value you choose is unique.