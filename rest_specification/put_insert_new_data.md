# PUT: Insert New Data
Insert new documents.  See [Insert](../language_specification/data.html#insert) in the Language Spec for
details of the document posted. Also look at [[Metada page|Rest-Spec-Metadata#rest-spec-metadata]]
to create the schema before trying to insert any data (the project require a defined schema before
any data be inserted, so it DOESN'T create schema automatically based on the insert).

### Request
Body of request is a JSON document matching the [request JSON schema](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/insertRequest.json).

Insert new data for given entity and specific version.
```
PUT /data/insert/{entityName}/{version}
{request JSON document}
```
---

Insert new data for given entity in the default version.
```
PUT /data/insert/{entityName}
{request JSON document}
```

### Response: Success
On success returns a [response JSON document](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/response.json).

### Response: Errors
* crud:NoAccess - caller doesn't have access required to insert the document
* crud:insert:NoFieldAccess - caller doesn't have acces required to insert a field
* mongo-crud:Duplicate - document already exists
* mongo-crud:SaveError - general error, unable to save document

### Example: create country

#### Request
```javascript
PUT /data/insert/country/1.0.0

{
    "objectType": "country",
    "version": "1.0.0",
    "data": [
        {
            "name": "Canada",
            "iso2code": "CA",
            "iso3code": "CAN"
        }
    ],
    "projection": [
        {
            "field": "*",
            "include": true
        }
    ]
}
```

#### Response
```javascript
{
    "status": "complete",
    "modifiedCount": 1,
    "processed": [
        {
            "name": "Canada",
            "iso2code": "CA",
            "iso3code": "CAN"
        }
    ]
}
```
