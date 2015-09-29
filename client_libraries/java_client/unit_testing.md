# Unit Testing with the Java Client

The Java Client further extens the [test harness](unit_testing/README.md) built into Lightblue and allows client applications to be able to stand and test against an in-memory instance of lightblue.

## AbstractLightblueClientCRUDController
Builds on the lightblue test harness by providing a Lightblue Client that is able to talk to the in-memory lightblue instance.

Features:
* Call the *getLightblueClient()* method to get a client that can speak to the lightblue instance.
* Provides a *loadData(...)* method to easliy load test data into entities.

## LightblueExternalResource
Exposes AbstractLightblueClientCRUDController as a JUnit Rule.

