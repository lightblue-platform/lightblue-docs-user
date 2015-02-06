# Configure

## datasources.json

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

## lightblue-metadata.json
At this time LDAP is not a suitable backend metadata store and cannot be used in this capacity.

## lightblue-crud.json
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
