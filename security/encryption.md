# Encryption
This section outlines how encryption of data can be done with lightblue.  While this is not a part of lightblue itself, it is included here for reference.

## In Transit - lightblue
Many containers today provide support for terminating SSL connections.  For example, we utilize JBoss EAP and have it configured to do this.

Configuration for this is done via puppet.  See class [lightblue::eap::ssl](https://github.com/lightblue-platform/lightblue-puppet/blob/master/manifests/eap/ssl.pp) in the [lightblue-puppet](https://github.com/lightblue-platform/lightblue-puppet) repository.

## In Transit - Mongo DB
The community version of Mongo DB starting with version 2.6 is built to include SSL capabilities.  MongoDB uses the MONGODB-CR challenge-response mechanism to authenticate the lightblue application user with a user name and password.  Note that use of Hiera allows the password for the lightblue application user to be encrypted.

Configuration for this is done via puppet.  See class [lightblue::eap::mongossl](https://github.com/lightblue-platform/lightblue-puppet/blob/master/manifests/eap/mongosslssl.pp) in the [lightblue-puppet](https://github.com/lightblue-platform/lightblue-puppet) repository.

## At Rest
Your individual deployment environment will dictate what is possible around disk and/or host encryption.  This section exists just to state that it is possible and something to evaluate based on the classification of data your deployment of lightblue will be storing.
