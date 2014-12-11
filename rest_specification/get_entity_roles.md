# GET: Entity Roles
Get list of all roles and the entities they allow access to.  See Request for details

### Request
Get all roles for given entity name and specific version.
```
GET /metadata/{entityName}/{version}/roles
```
---

Get all roles for given entity name and default version.  If entity doesn't have a default vesrion an error is returned.
```
GET /metadata/{entityName}/roles
```
---

Get all roles for all entities with default version.  If entity doesn't have a default version an error is returned for that entity.
```
GET /metadata/roles
```

### Response: Success
Returns an array of objects that follow this JSON structure.  Note there is no JSON-schema for this at this time, subject to change.
```javascript
    [
        {
            "role": the role,
            "insert": [array of paths],
            "find": [array of paths],
            "update": [array of paths],
            "delete": [array of paths]
        }
    ]
```
array of paths - each "path" starts with at least an entity name.  If there is no sub-path it is access to the full entity.  If it contains a sub-path it is access to a specific field on that entity.

#### Example: user.find
```javascript
    [
        {
            "role": "user.find",
            "find": ["user"]
        }
    ]
```
#### Example: user.credentials.write
```javascript
    [
        {
            "role": "user.credentials.write",
            "insert": ["user.credentials"],
            "find": ["user.credentials"],
            "update": ["user.credentials"],
            "delete": ["user.credentials"]
        }
    ]
```
#### Example: user.credentials.read
```javascript
    [
        {
            "role": "user.credentials.read",
            "find": ["user.credentials"]
        }
    ]
```
### Response: Errors
Additional error codes:
* metadata:MissingEntityInfo - if entityName is supplied, entity info does not exist
* metadata:MissingSchema - if version is supplied, schema does not exist for given entity name + version
* ERR_NO_METADATA - the entity doesn't have default version
