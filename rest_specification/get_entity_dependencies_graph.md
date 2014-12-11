# GET: Entity Dependencies Graph
Get a "graph" of dependencies between entities.  Default versions are used unless a specific entity with version is requested.  If no version is specified and the entity has no default version the dependencies are not returned for that entity.

### Request
Get dependencies for:
* entityName - the name of entity
* version - the specific version

```
GET /metadata/{entityName}/{version}/dependencies
```
---

Get dependencies for entity with default versions.
```
GET /metadata/{entityName}/dependencies
```
---

Get dependencies for all entities with default versions.
```
GET /metadata/dependencies
```
### Response: Success
Returns an array of objects that follow this JSON structure.  Note there is no JSON-schema for this at this time, subject to change.
```javascript
[
    {
        "name": entityName,
        "dependencies": [array of 'this' structure]
    }
]
```

#### Example: cyclic
```javascript
[
    {
        "name": "foo",
        "dependencies": [
            {
                "name": "bar",
                "dependencies": [
                    {
                        "name": "baz"
                    },
                    {
                        "name": "foo"
                    }
                ]
            }
        ]
    }
]
```

### Response: Errors
Additional error codes:
* metadata:MissingEntityInfo - if entity name was specified, entity info does not exist
* metadata:MissingSchema - if version was specified, schema does not exist for given entity name + version
