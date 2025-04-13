Sets the form POST mode for requests specifically for **key value pair** form submissions that use `AddPostKey(lcKey, lcValue)`. This property only applies to **Url Encoded** (`application/x-www-form-urlencoded`) or **Multi-Part** (`multipart/form-data`) form data not to full document types like `application/json` or `text/xml` for example.

For any other full document content type operations that use `AddPostKey(lcValue)` - like `application/json` or `text/xml` - set the [cContentType](vfps://Topic/wwHTTP%3A%3AcContentType) property **instead**. 

> Do not use both `nHttpPostMode` and `cContentType` - use one or the other.

Available form post modes for key value data are:

| Value | Description                              | Content Type                      |
|-------|------------------------------------------|-----------------------------------|
| 1*    | Form Url Encoded (typical POST) | application/x-www-form-urlencoded |
| 2     | Multipart forms (file uploads)           | multipart/form-data               |

These settings apply only for calls to `AddPostKey(key, value)` when `key` and `value` parameters are passed. These key value pairs are then appended into the POST buffer that is built up with each call to `AddPostKey(key,value)`.

> For all other **raw buffer data like JSON or XML or binary files** passed use the [cContentType Property](vfps://Topic/wwHTTP%3A%3AcContentType) instead.

### Form POST Value Encodings

**Url Encoded Form**  
<small>*application/x-www-form-urlencoded*</small>  
Url encoded uses key value pairs as Url Encoded content which looks like this as a POST buffer:

```text
name=Rick%20Strahl&company=West%20Technologies
```

It's the equivalent of an HTML form that submits with:

```html
<form action="http://localhost:8000" method="post">
```
    
**Multi-Part Form (application/x-url-encoded)**    
<small>*multipart/form-data*</small>  
Multipart forms are generally used for file uploads or other binary data to post from HTML forms. It tends to be more efficient than Url Encoded, because the actual data is not encoded and sent raw. Instead every variable has a MIME header that describes the content. Each content block is raw text or binary data.

```text
----------------------------9051914041544843365972754266
Content-Disposition: form-data; name="text"

text default
-----------------------------9051914041544843365972754266
Content-Disposition: form-data; name="file1"; filename="a.txt"
Content-Type: text/plain

Content of a.txt.

-----------------------------9051914041544843365972754266
Content-Disposition: form-data; name="file2"; filename="a.html"
Content-Type: text/html

<!DOCTYPE html><title>Content of a.html.</title>
```

It's equivalent to form submission from an HTML form:

```html
<form action="http://localhost:8000" method="post" enctype="multipart/form-data">
```

### Only use for Key Value Pair POST data    
`nHttpPostMode=2` or `nHttpPostMode=4` are meant for the specific use case of send HTML style form data using key value pairs using `AddPostKey(key,value)` append to the post data already present in the buffer.

For any other content types or full 'documents' of data **always use the [cContentType](vfps://Topic/wwHTTP%3A%3AcContentType) property instead.
    

### Default Mode
If neither `nHttpPostMode` or `cContentType` are set, the default POST mode is `nHttpPostMode=2` which is url encoded form data with  a content type of `application/x-www-form-urlencoded`.