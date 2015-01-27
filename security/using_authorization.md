# Using Authorization
This section outlines how authorization is used by lightblue.  It is assumed in this section that authorization is enabled.

## Metadata Management Application

The metadata management application gets authorization only from SAML assertions.  If SAML authentication is not enabled, this application does not have another way to get authorization information at this time.

The metadata management application uses authorization to decide what sections of the user interface should be visible to the authenticated user.  By default any authenticated user is authorized to see the read-only operations in the application.

## Data Management Application

As noted in the earlier [Authentication and Authorization](../authentication_and_authorization.md) section, the data management application is a lightweight facade on top of the data REST application.

### Metadata REST Application
Each operation in the metadata service can be configured to require specific roles for the authenticated user.  This configuration is a simple map of each operation to a list of roles that will be accepted for that operation.

Configuration for this is done via puppet.  See class [lightblue::eap::module::metadata](https://github.com/lightblue-platform/lightblue-puppet/blob/master/manifests/eap/module/metadata.pp) in the [lightblue-puppet](https://github.com/lightblue-platform/lightblue-puppet) repository.

## Data REST Application
The data service provides CRUD (Create, Read, Update, and Delete) operations for lightblue.  To secure access to data, lightblue has the ability to define permissions for each operation at both the entity and field levels.  The permissions are stored in the entity metadata and are enforced by lightblue.  All deployments of lightblue can leverage two special permissions regardless of the authorization scheme deployed: `anyone` and `noone`.

* `anyone` - any authenticated user is authorized to access the entity or field
* `noone` - no user is authorized to access the entity of field
