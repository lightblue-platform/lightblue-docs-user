# GET: Entity Names
Get names of all defined entities.  There is no paging for this request, all entity names are returned in one response.

### Request
Get all entity names with schemas in the given status list.  List is a comma separate lis of: active, deprecated, and/or disabled.
```
GET /metadata/s={comma separated status list (active, deprecated, disabled)}
```
---

Get all entity names.
```
GET /metadata
```

### Response: Success
JSON document that is an array of strings.  Each element in the array is an entity name.
