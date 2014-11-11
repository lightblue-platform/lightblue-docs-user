# Audit Hook
Hooks are a way to have trigger-like capabilities in lightblue that span any datastore.  Lightblue provides at least one hook for auditing changes to data.  There is a set of core capabilities around hooks that is provided with core lightblue.

The Audit Hook is a specific hook implementation that can be used to track changes to an entity's data over time.  This document covers how to configure the audit hook for an entity to track a subset of fields.  It uses the `country` entity used in [openshift-lightblue-cart](https://github.com/lightblue-platform/openshift-lightblue-cart).

Source for the audit hook is in the [lightblue-audit-hook](https://github.com/lightblue-platform/lightblue-audit-hook) repository.

Covered in this chapter:
* **Configure Audit Hook Parser** - enabling audit hook
* **Initial Metadata** - base metadata without hooks
* **Audit All Changes** - audit all changes to the entity
* **Audit Subset of Changes** - restrict what is audited with a projection
