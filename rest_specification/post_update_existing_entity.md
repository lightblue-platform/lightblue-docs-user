# POST: Update Existing Entity
Update subset of documents based on the given query.  See [Update](../language_specification/data.html#update) in the Language Spec for details of the document posted.

### Request
Body of request is a JSON document matching the [request JSON schema](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/updateRequest.json).

Update data for given entity and specific version.
```
POST /data/update/{entityName}/{version}
{request JSON document}
```
---

Update data for given entity using default version.
```
POST /data/update/{entityName}
{request JSON document}
```

### Response: Success
On success returns a [response JSON document](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/response.json).

### Response: Errors
* crud:NoAccess - caller doesn't have access required to save the document
* crud:insert:NoFieldAccess - caller doesn't have acces required to save a field
* mongo-crud:NullQuery - query in request is missing
* mongo-crud:Duplicate - document already exists
* mongo-crud:UpdateError - general error, unable to update document
