# Metadata

As with other plugins, the metadata is made up of two parts, the _entityInfo_ and _schema_. See the documentation on Metadata for more high level details on how these are used. Continue reading for LDAP specifics.

More examples can be found in the **lightblue-ldap-integration-test** module.

## entityInfo

**Attributes:**
* **backend** - Name of the datasource provider to use. Should always be "ldap".
* **database** - Name of database to associate this entity too. Should match the _database_ attribute in _datasources.json_.
* **basedn** - The base _dn_ for where the entities should live within the LDAP directory structure.
* **uniqueaddr** - The LDAP attribute that will indicate uniqueness for instances of this entity. Lightblue-LDAP will use this in the _dn_ when creating or reading entities. For example (based on below): uid=jdoe.ou=Users,dc=example,dc=com

```json
{
    "entityInfo": {
        "name": "entityName",
        "datastore": {
            "backend":"ldap",
            "database": "LdapDatabaseName",
            "basedn": "ou=Users,dc=example,dc=com",
            "uniqueattr": "uid"
        }
    },
    "schema": {...}
}
```

### Alias Attribute Names
You may also alias attribute names if you wish for the entity fields to be different. To do so, simply add an _ldap_ section to the _entityInfo_ and define the mappings.

In the following example, the LDAP attribute "LdapAttributeName" will be aliased as "EntityFieldName" in the remainder of the metadata definition and in all CRUD operations.

**NOTE:** The actual attributed name will no longer be available for use and can only be referenced by the aliased name.

**EXCEPTION TO ABOVE NOTE:** The _uniqueattr_ **MUST** continue to be the actual attribute name, but can be aliased for field definitions and CRUD operations.
```json
{
    "entityInfo": {
        "name": "...",
        "datastore": {...}
        "ldap": {
            "fieldsToAttributes": [
                {
                    "field": "EntityFieldName",
                    "attribute": "LdapAttributeName"
                },
                ...
            ]
        }
    },
    "schema": {...}
}
```

## schema

Nothing too interesting here, as this follows the standard schema definition.

From an LDAP perspective, you might notice that _dn_ and _objectClass_ are not included in the _fields_ list, and that is because they will automatically be added and available for CRUD operations. You are welcome to specify them yourself if you prefer, but it is unnecessary.

**NOTE:** The attribute identified as the _uniqueattr_ in the _entityInfo_ section is required to be defined in the _fields_ list.

**NOTE:** If you chose to alias the field names in the _entityInfo_ section, then the field names would be the "EntityFieldName" as in the earlier example.

```json
{
    "entityInfo": {...}
    "schema": {
        "name": "entityName",
        "version": {
            "value": "1.0.0",
            "changelog": "blahblah"
        },
        "status": {
            "value": "active"
        },
        "access" : {
             "insert": ["anyone"],
             "update": ["anyone"],
             "delete": ["anyone"],
             "find": ["anyone"]
        },
        "fields": {
            "uid": {"type": "string"},
            "givenName": {"type": "string"},
            "sn": {"type": "string"},
            "cn": {"type": "string"}
        }
    }
}
```
