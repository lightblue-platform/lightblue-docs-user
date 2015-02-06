# CRUD Operation Examples

More examples can be found in the lightblue-ldap-integration-test module.

## Insert

The _objectClass_ does need to be populated

**NOTE:** Insert can only project _dn_ at this time.

```json
{
    "entity": "entityName",
    "entityVersion": "1.0.0",
    "projection": {
        "field": "dn"
    },
    "data": [
        {
            "objectClass": ["top", "person", "organizationalPerson", "inetOrgPerson"],
            "uid": "john.doe",
            "givenName": "John",
            "sn": "Doe",
            "cn": "John Doe"
        },
        ...
    ]
}
```

## Find

**NOTE:** Sorts cannot be performed by _dn_, because it is technically not an attribute.

```json
{
    "entity": "entityName",
    "entityVersion": "1.0.0",
    "projection": [
        {"field": "dn"},
        {"field": "uid"},
        {"field": "objectType"},
        {"field": "objectClass#"}
    ],
    "query": {
        "field": "uid",
        "op": "$eq",
        "rvalue": "john.doe"
    }
}
```

## Save
Not yet implemented

## Update
Not yet implemented

## Delete
Not yet implemented
