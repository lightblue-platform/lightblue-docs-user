# POST: Delete Data
Delete documents based on the given query.  See [[Delete|Language-Spec-Data#wiki-delete]] in the Language Spec for details of the document posted.

### Request
Body of request is a JSON document matching the [request JSON schema](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/deleteRequest.json).

Delete data for given entity and specific version.
```
POST /data/delete/{entityName}/{version}
{request JSON document}
```
---

Delete data for given entity using default version.
```
POST /data/delete/{entityName}
{request JSON document}
```

### Response: Success
On success returns a [response JSON document](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/response.json).

### Response: Errors
* crud:NoAccess - caller doesn't have access required to save the document
* mongo-crud:NullQuery - query in request is missing
