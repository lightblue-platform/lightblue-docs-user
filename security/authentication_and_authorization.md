# Authentication and Authorization

There are two ways you can configure your deployment of lightblue for authentication.  The easiest is without any authentication.  This means deploying the unauthenticated management artifacts and not configuring any security domains in your container.  If you require authentication, the following sections outline the existing options.

## REST Applications

The REST applications currently support client certificate authentication.

Client certificate authentication is enabled by configuring JBoss security to require a certificate be presented by the client.  Use of client certificate authentication currently requires authorization to be enabled as well.  The only authorization scheme available at this time is LDAP based.

Configuration for this is done via puppet.  See the following classes in the [lightblue-puppet](https://github.com/lightblue-platform/lightblue-puppet) repository:
* [lightblue::authentication::certificate](https://github.com/lightblue-platform/lightblue-puppet/blob/master/manifests/authentication/certificate.pp)
* [lightblue::eap::module](https://github.com/lightblue-platform/lightblue-puppet/blob/master/manifests/eap/module.pp) - specifically deployment of cacert.pem and lb-metadata-mgmt.pkcs12

## Metadata Management Application

The metadata management application currently support SAML authentication.

SAML authentication is enabled by configuring a JBoss security domain and deploying the SAML enabled version of the applications:
* [lightblue-applications-metadata-mgmt-saml-auth](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.redhat.lightblue.applications%22%20AND%20a%3A%22metadata-mgmt-saml-auth%22)

Authorization for SAML is provided as roles on the SAML assertion.  These roles are all that will be used for authorization of the user in this scenario.

Configuration for this is done via puppet.  See class [lightblue::authentication::saml](https://github.com/lightblue-platform/lightblue-puppet/blob/master/manifests/authentication/saml.pp) in the [lightblue-puppet](https://github.com/lightblue-platform/lightblue-puppet) repository.

## Data Management Application

The data management application is a lightweight client on top of the data REST application. If authentication is enabled for the data REST application the user must import their certificate into the browser used to access the data management application.  All requests made via the data management application are then a direct call from the user's browser to the data REST application.
