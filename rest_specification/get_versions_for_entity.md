# GET: Versions for Entity
Get a list of all available versions of a given entity.

### Request
One parameter as a path param of the request.
```
GET /metadata/{entityName}
```

### Response: Success
JSON document that is an array of version information objects.
```javascript
[
  {"version": versionValue,
   "changelog": string,
    "extendsVersions": [ version, ...],
    "status": "active"|"disabled"|"deprecated",
    "defaultVersion": true|false
  },
    ...
]
```

### Response: Errors
Additional error codes:
* metadata:NoEntityName - no entity name specified
