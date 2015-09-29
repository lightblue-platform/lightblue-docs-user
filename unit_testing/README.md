# Unit Testing with Lightblue

Lightblue comes with a built in testing harness that can stand an in-memory instance of lightblue powered by the mongo backend. This is useful for unit testing new backend controllers and hooks.

The Lightblue Client further builds on the harness in order to provide this functionality to applications built on the lightblue platform. That documentation can be found [here](client_libraries/java_client/unit_testing.md).

## AbstractCRUDTestController
This is the base class that makes the in-memory instance of lightblue possible, but it is not very useful by itself as it lacks a backend. It contains utilities for configuring lightblue, loading metadata, and creating requests.

Features:
* Loads metadata into lightblue.
* By default strips out all hooks from metadata
* By default will recursively sets all access roles to 'anyone'.
* Is able to set the datasource on all metadata ensuring they are always the same.
* Can load lightblue per suite (statically), or per test.
 * Caveot: mongo abstraction only loads per suite.

## AbstractMongoCRUDTestController
Stands an in-memory instance of Mongo as the primary backend for lightblue. As of this writing, it is the only abstraction that provides a primary backend, and should be considered the parent for most or all controller and hook test suites.

Features:
* Establishes a set of overridable system properties that can be used as substitution values in mongo metadata. Substitution uses ${} notation.
* Utility methods for interacting with mongo.
* Mongo configuration can be used by adding the *@InMemoryMongoServer* annotation to the sub-class.

Thing to be aware of:

The 'mongo-' prefix is added to all lightblue-metadata and datasources configuration json files. This was originally done in an attempt to prevent default implementations of these files from colliding, but the default implementations have sense been removed. Now these files must be provided in the src/test/resources directory of each tested application.

## AbstractCRUDControllerWithRest
Further builds on the test harness by exposing lightblue as an http server.

Features:
* Allows the port that the in-memory lightblue http server runs on.
