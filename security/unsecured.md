# Unsecured Deployment
Not every deployment needs security enabled.  Proof of concept and possibly development environments come to mind as possible examples.  The [downloadable openshift ligthblue cartridge](https://github.com/lightblue-platform/openshift-lightblue-cart) is and example of an unsecured deployment.

If you deployment does not require any authentication or authorization:
* do not deploy any container security modules for the REST API's
* deploy the unauthenticated versions of the managment applications:
    * lightblue-applications-data-mgmt
    * [lightblue-applications-metadata-mgmt](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.redhat.lightblue.applications%22%20AND%20a%3A%22metadata-mgmt%22)
