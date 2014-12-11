# Error

All errors are reported using the following error object:
```javascript
{
  "object_type" : "error",
  "context" : "c1/c2/c3/...",
  "errorCode" :  "SomeErrorCode",
  "msg" : "Error message"
}
```
* object_type: Always "error"
* context: A string delimited with "/" characters that define
  the context of execution where the error occurred, similar
  to a stack trace.
* errorCode: Error code string
* msg: Somewhat user friendly error message
