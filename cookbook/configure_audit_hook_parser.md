# Configure Audit Hook Parser
By default, no hooks are enabled.  To enable the audit hook the `metadata mediator` needs to be configured.  This is done by adding the desired hook configuration parser to the `lightblue-metadata.json` file in your lightblue module.  These parsers are configured by adding a property `hookConfigurationParsers` that has an array of strings that are the full class names of each of the parsers.

An example configuration that has just the audit hook configration parser:

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

**Note**, if you use Puppet and Hiera, set the following in your Hiera hierarchy somewhere:

```
lightblue::eap::module::hook_configuration_parsers:
    - com.redhat.lightblue.hook.audit.AuditHookConfigurationParser
```
