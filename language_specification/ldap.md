# LDAP
## Overview
<a href="https://github.com/lightblue-platform/lightblue-ldap">Lightblue-LDAP</a> is a plugin that provides lightblue the ability to store and retrieve data to/from <a href="http://en.wikipedia.org/wiki/Lightweight_Directory_Access_Protocol">Lightweight Directory Access Protocol (LDAP)</a> backends.

This article explains how to configure the LDAP plugin and provides basic usage examples.

## Configure

### datasources.json

In order to connect lightblue to LDAP, you must first add a datasource to _datasources.json_.

Multiple LDAP datasources can be used by adding additional blocks, each with a unique datasource and database name.

**Attributes:**
 * **type** - Class path to the _DataSourceConfiguration_ implementation for LDAP. Should always be _com.redhat.lightblue.config.ldap.LdapDataSourceConfiguration_.
 * **database** - Arbitrary name for the database, used by metadata to reference the target database.
 * **bindabledn** - Similar to a username, this is the _dn_ used to authenticate into LDAP.
 * **password** - Password associated with the _bindabledn_.
 * **numberOfInitialConnections** - _(optional)_ The number of connections to initially establish when the pool is created. Defaults to 5.
 * **maxNumberOfConnections** - _(optional)_ The maximum number of connections that should be maintained in the pool. Defaults to 10.
 * **servers** - A list of _host_ and _port_ numbers to connect to. At least one must be provided. Multiples indicate mirrors that can be equally read from and written to.

Example:
```json
{
    "MyLdapDatasourceName": {
        "type" : "com.redhat.lightblue.config.ldap.LdapDataSourceConfiguration",
        "database" : "LdapDatabaseName",

        "bindabledn" : "uid=admin,dc=example,dc=com",
        "password" : "secret",
        "numberOfInitialConnections" : 5,
        "maxNumberOfConnections" : 10,
        "servers" : [
            {
                "host" : "localhost",
                "port" : "389"
            }
        ]
    }
}
```

### lightblue-metadata.json
At this time LDAP is not a suitable backend metadata store and cannot be used in this capacity.

### lightblue-crud.json
Add the following block to the _controllers_ section in _lightblue-crud.json_. There really isn't anything to configure here, so the equivalent of a copy/paste should work.

```json
{
   "controllers" : [
      {
        "backend" : "ldap",
        "controllerFactory" : "com.redhat.lightblue.config.ldap.LdapControllerFactory"
      }
   ]
}
```

## Metadata

As with other plugins, the metadata is made up of two parts, the _entityInfo_ and _schema_. See the documentation on Metadata for more high level details on how these are used. Continue reading for LDAP specifics.

More examples can be found in the **lightblue-ldap-integration-test** module.

### entityInfo

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

#### Alias Attribute Names
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

### schema

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

## CRUD Operation Examples

More examples can be found in the lightblue-ldap-integration-test module.

### Insert

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

### Find

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

### Save
Not yet implemented

### Update
Not yet implemented

### Delete
Not yet implemented

## General Notes
* _dn_ is assumed to be downcased
* _objectClass_ is assumed to be cased as such
