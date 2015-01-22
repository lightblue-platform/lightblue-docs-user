# POST: Save Existing Entity
Replace the contents of the given documents with new data.  See [Save](../language_specification/data.html#save) in the Language Spec for details of the document posted.

### Request
Body of request is a JSON document matching the [request JSON schema](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/saveRequest.json).

Save data for given entity and specific version.
```
POST /data/save/{entityName}/{version}
{request JSON document}
```
---

Save data for given entity using default version.
```
POST /data/save/{entityName}
{request JSON document}
```

### Response: Success
On success returns a [response JSON document](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/response.json).

### Response: Errors
* crud:NoAccess - caller doesn't have access required to save the document
* crud:insert:NoFieldAccess - caller doesn't have acces required to save a field
* mongo-crud:Duplicate - document already exists
* mongo-crud:SaveError - general error, unable to save document
