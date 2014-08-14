# Custom Hook
Hooks are a way to have trigger-like capabilities in lightblue that span any datastore.  Lightblue provides at least one hook for auditing changes to data.  There is a set of core capabilities around hooks that is provided with core lightblue.  This document gives an overview of what was done to create the `AuditHook` implementation and how to configure lightblue to use it.

Source for the audit hook is in the [lightblue-audit-hook](https://github.com/lightblue-platform/lightblue-audit-hook) repository, can be referenced for this cook book.


## Overview
Core hook code is broken into the corresponding `metadata` and `crud` components as part of `lightblue-core`.

* `metadata` - manages collections of hooks and their configuration
* `crud` - the details of hook implementation

To make a hook you need:
* `lightblue-core/metadata`
    * an implementation of `com.redhat.lightblue.metadata.HookConfiguration`
    * an implementation of `com.redhat.lightblue.metadata.parser.HookConfigurationParser`
* `lightblue-core/crud`
    * an implementation of `com.redhat.lightblue.hooks.HookResolver`
    * your hook implementation that extends `com.redhat.lightblue.hooks.CRUDHook`

Specifically saying "an implementation" above because there could be an existing implementation that is usable.

## Example: the implementation of AuditHook

In the following sections you will find more details how Hook implementation and configuration works using the AuditHook as an example.

### AuditHookConfiguration
The hook configuration is purposefully not implemented because there is no schema.  Your hook is likely going to need very specific configuration options.  Therefore it's left to the hook implementer.  There are no required attributes.  For each configuration a corresponding parser must exist.  There's no requirements for configurations, but recommendations are:
* make the implementation immutable
* do not contain any business logic

For the audit hook the configuration needs the following:
* entityName - the entity where the audit data is stored
* version - the version of the entity

The implementation is simple and immutable with:
* constructor taking all attributes
* getters for all attributes
* implements `HookConfiguration`

### AuditHookConfigurationParser
Hook configuration parsers take an input object type (set via generics) and create the specific hook configuration implementation. In this case the parser:
* takes a `JsonNode` input
* returns a `AuditHookConfiguration` implementation
* uses `MetdataParser` argument to get fields from the input

### Metadata Configuration
To get this working, the `metadata mediator` needs to be configured. It knows about parsers via the `Extensions` class. An example of how this can be configured manually is found in [AuditHookConfigurationParserTest](https://github.com/lightblue-platform/lightblue-audit-hook/blob/master/src/test/java/com/redhat/lightblue/hook/audit/AuditHookConfigurationParserTest.java).

At the most basic level the `lightblue-metadata.json` file in your lightblue module needs to be told about the parser.  It is set by putting a property `hookConfigurationParsers` that has an array of strings that are the full class names of each of the parsers.

```
{
    "documentation": [
        "type - REQURED - the class implementing MetadataConfiguration interface",
        "hookConfigurationParsers - REQUIRED - array of classes implementing HookConfigurationParser interface",
        "The remainder of the file is parsed by the implementation class"
    ],
    "type": "com.redhat.lightblue.mongo.config.MongoMetadataConfiguration",
    "hookConfigurationParsers": ["com.redhat.lightblue.config.AuditHookConfigurationParser"],
    "dataSource": "metadata",
    "collection": "metadata"
}
```

To do the same with Puppet and Hiera, set the following in your Hiera hierarchy somewhere:

```
lightblue::eap::module::hook_configuration_parsers:
    - com.redhat.lightblue.config.AuditHookConfigurationParser
```

# TO BE CONTINUED
CRUD bits to come later.. they're still under development!

Notes for future documentation.

## AuditHook
* extends `CRUDHook`
* business logic to
    * check for changes
    * persist changes

