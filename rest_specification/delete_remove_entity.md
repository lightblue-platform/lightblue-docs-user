# DELETE: Remove Entity
Delete given entity.  Does not delete data for the entity.  Requires all versions for the entity to be disabled.
```
DELETE /metadata/{entityName}
```

### Response: Success
Empty string

### Response: Errors
Additional error codes:
* metadata:NoEntityName - no entity name specified
* metadata:CannotDeleteEntity - Entity cannot be deleted
