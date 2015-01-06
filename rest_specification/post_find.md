# POST: Find
Find documents based on the given query.  See [Find](../language_specification/data.html#find) in the Language Spec for details of the document posted.  Note that if caller requests a field they are not authroized to access the request will not fail.  Instead the field will simply not be returned in the response.

### Request
Body of request is a JSON document matching the [request JSON schema](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/findRequest.json).

Find data for given entity and version.
```
POST /data/find/{entityName}/{version}
{request JSON document}
```
---

Find data for given entity using default version.
```
POST /data/find/{entityName}
{request JSON document}
```

### Response: Success
On success returns a [response JSON document](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/response.json).

### Response: Errors
* crud:NoAccess - caller doesn't have access required to save the document
* mongo-crud:NullQuery - query in request is missing
* mongo-crud:NullProjection - projection in request is missing

### Example: find country

#### Request
```javascript
POST /data/find/country/1.0.0

{
    "objectType": "country",
    "version": "1.0.0",
    "query": {
        "field": "iso2code",
        "op": "=",
        "rvalue": "US"
    },
    "projection": [
        {
            "field": "name",
            "include": true
        },
        {
            "field": "iso3code",
            "include": true
        }
    ]
}
```

#### Response
```javascript
{
    "status": "complete",
    "matchCount": 1,
    "processed": [
        {
            "name": "United States of America",
            "iso3Code": "USA"
        }
    ]
}
```
