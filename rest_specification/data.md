# Data

* Descriptive name is the Header
* First paragraph is a short description of the API
* Request section contains info on what the request looks like
* Response section contains info on what the response looks like, including errors where applicable
* Example section contains a successful example and an unsuccessful example.

## PUT: Insert New Data
Insert new documents.  See [Insert](../language_specification/data.html#insert) in the Language Spec for
details of the document posted. Also look at [[Metada page|Rest-Spec-Metadata#rest-spec-metadata]]
to create the schema before trying to insert any data (the project require a defined schema before
any data be inserted, so it DOESN'T create schema automatically based on the insert).

### Request
Body of request is a JSON document matching the [request JSON schema](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/crud.json).

Insert new data for given entity and specific version.
```
PUT /data/{entityName}/{version}
{request JSON document}
```
---

Insert new data for given entity in the default version.
```
PUT /data/{entityName}
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
```
PUT /data/insert/country/1.0.0

{
    "entity": "country",
    "entityVersion": "1.0.0",
    "data": [
        {
            "name": "Canada",
            "iso2code": "CA",
            "iso3code": "CAN"
        }
    ],
    "returning": [
        {
            "field": "*",
            include: true
        }
    ]
}
```

#### Response
```
{
    "status": "complete",
    "modifiedCount": 1,
    "response": [
        {
            "name": "Canada",
            "iso2code": "CA",
            "iso3code": "CAN"
        }
    ]
}
```

## POST: Save Existing Entity
Replace the contents of the given documents with new data.  See [Save](../language_specification/data.html#save) in the Language Spec for details of the document posted.

### Request
Body of request is a JSON document matching the [request JSON schema](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/crud.json).

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

## POST: Update Existing Entity
Update subset of documents based on the given query.  See [Update](../language_specification/data.html#update) in the Language Spec for details of the document posted.

### Request
Body of request is a JSON document matching the [request JSON schema](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/crud.json).

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

## POST: Delete Data
Delete documents based on the given query.  See [[Delete|Language-Spec-Data#wiki-delete]] in the Language Spec for details of the document posted.

### Request
Body of request is a JSON document matching the [request JSON schema](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/crud.json).

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

## GET: Find (Simple)

A simplified find API that takes just query parameters to do a simple search.

```
GET /data/{entity}?Q&P&S&R
```
```
GET /data/{entity}/{version}?Q&P&S&R
```

where Q represents the query, P represents the projection, S represents the sort expressions, and R is the range.

Query expression:
```
 q=field1:value1,value2[;field2:value...]
```
Only value equivalence is supported.

Projection expression:
```
  p=field2:1,field3:0,...
```
:1, included, :0, excluded. Patterns can be used. :1r recursive inclusion, :0r recursive exclusion.

Sort expression:
```
  s=field1:a,field2:d,...
```
:a means ascending, :d means descending

Result Range:
```
from=<number>&to=<number>
```
If from is not given, result set starts from 0. If to is not given, all results are returned (up to a cap, given in configuration).


## POST: Find
Find documents based on the given query.  See [Find](../language_specification/data.html#find) in the Language Spec for details of the document posted.  Note that if caller requests a field they are not authroized to access the request will not fail.  Instead the field will simply not be returned in the response.

### Request
Body of request is a JSON document matching the [request JSON schema](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/crud.json).

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
```
POST /data/find/country/1.0.0

{
    "entity": "country",
    "entityVersion": "1.0.0",
    "query": {
        "field": "iso2code",
        "op": "=",
        "rvalue": "US"
    },
    "returning": [
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
```
{
    "status": "complete",
    "matchCount": 1,
    "response": [
        {
            "name": "United States of America",
            "iso3Code": "USA"
        }
    ]
}
```

## Future Functionality
In the future we might implement the following, but have no plans at this time.

### PUT: Save Query
Save a query for reuse.  Will require some design, but possibly a query with some bind parameteres that can be used in the Find with Saved Query API.

### GET: Find with Saved Query
Uses a saved query and optional bind parameters instead of posting the full query every time.
