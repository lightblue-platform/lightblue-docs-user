# Plugins

A plugin is an extension to the lightblue platform that adds some functionality that is not otherwise provided out-of-the-box. This would most likely be a hook (eg. audit hook) or a new backend (eg. ldap).

## Installation

It is the responsibility of the system owner to install the plugin's jar files on the host. There is no standard installation directory, but *'/usr/share/java/lightblue/project_name'* is recommended.

## Configuration

If using plugins, the optional configuration file *'lightblue-plugins.json'* will need to exist in *'/usr/share/jbossas/modules/com/redhat/lightblue/main/'*. This json file should contain an array of text nodes that each have the file path to a single jar or a directory of jars (recursively searched).

```json
[
  "/my/directory/of/jars",
  "/a/path/to/my.jar"
]
```

**NOTE:** lightblue-puppet can land this file if an array of paths is passed into **lightblue::eap::module:plugins**.

**TIP:** If the jar files exist with a top level directory, then just the single top level directory path is needed.

Standard *lightblue-crud.json*, *lightblue-metadata.json*, and *datasources.json* configuration is still required in order to make lightblue aware of the plugin's classes.

## What about dependencies?

Include any jar files the plugin produces or any 3rd party libraries required by the plugin (eg. lightblue-mongo needs a mongo client in order to work).

Do not include lightblue-core or any dependencies already provided by the lightblue runtime. 