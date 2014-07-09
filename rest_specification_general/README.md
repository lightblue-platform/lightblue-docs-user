# REST Specification: General

## Authentication
Authentication is not the responsibility of lightblue.  Any request that calls a REST application is assumed to be authenticated.  This means something like the container or a local proxy (httpd) is intercepting all requests and verifying authentication before passing the request on.  Examples of how to do this may be added to this document later.

## Authorization
For each request lightblue requires authorization information about the client.  This is captured as a set of "roles" associated with that client.  Management of those roles is outside the scope of lightblue.  Lightblue will simply look for roles in a specific location on each request.  Examples of how to do this may be added to this document later.

### Roles
Authentication/Authorization implementation is expected to retrieve the roles of the client, and pass those roles to the lower layers of Lightblue.

## HTTP Headers
In order to have a consistent way of extracting metadata from a request we have decided to put that data into HTTP headers.  This could be done by the caller, by a proxy, by a login module, etc.  Each header name is managed in configuration using [Archaius](https://github.com/Netflix/archaius).  The property name and default values are documented below:
* Principal - the login of the user or system calling the service
    * Default = lightblue_principal
    * Property = httpheader.principal
* Common Name - the "common" name of the principal
    * Default = lightblue_commonname
    * Property = httpheader.commonname
* Entity Name - name of the entity requested
    * Default = lightblue_entityname
    * Property = httpheader.entityname
* Entity Version - version of the entity requested
    * Default = lightblue_entityversion
    * Property = httpheader.entityversion

## Error Response Object
See [error JSON-schema](https://raw.github.com/bserdar/lightblue/master/query-api/src/main/resources/json-schema/error/error.json) for details.

Each response section for documenting errors will include mention of the expected error codes only.  The fields are:
* object_type is always "error"
* context is the place in processing at which the code failed
* msg is a descriptive message that should help a developer understand the error

There are some common error codes that could be returned on any request and they are:
* metadata:ConfigurationNotFound - configuration for the metadata manager was not found
* metadata:ConfigurationNotValid - configuration for the metadata manager is not valid
* rest-metadata:RestError - generic error when no other specific error can be determined

    {
        "object_type": "error",
        "context": "error/context/path",
        "errorCode": "DESCRIPTIVE_ERROR_CODE",
        "msg": "User friendly error message."
    }
