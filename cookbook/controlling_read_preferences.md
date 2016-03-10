# Controlling Read Preferences

Read preferences for distributed datastores is is managed by the individual datasource configuration.  Not every datastore will support a read preference since it's a function of distributed databases.

In the world of distributed there are two flavors we care about here:
1. Multi Master
2. Single Master

## Multi-Master
With a multi-master database you have more than one database instance that can accept writes.  There are pros and cons to this type of system and it's well discussed the [Multi-master Replication](https://en.wikipedia.org/wiki/Multi-master_replication) wikipedia document.

## Single-Master
With a distributed single master database a benefit is no longer worrying about conflict resolution.  But the tradeoff is clients must be able to handle [eventual consistency](https://en.wikipedia.org/wiki/Eventual_consistency) or handle latency to the primary.

Scenario:

1. Database backing a lightblue entity distributed in two datacenters: A and B.
2. Lightblue data service is deployed in two datacenters: A and B.
2. Database has a single master in datacenter A.
3. Application is deployed in datacenter B.

The client application has a choice:
* accept reading potentially stale data from a local read-only instance in datacenter B
* accept latency to the master in datacenter A to get up to date data

This is a problem specifially with the Mongo Controller as MongoDB has a single primary node in a replica set that accepts writes.  Lightblue can be configured to prefer fast reads or consistent reads.

### Default: Fast Reads

The lightblue data services are configured with the following [read preferences](https://docs.mongodb.org/manual/core/read-preference/):
* lightblue data service in datacenter A (primary): primaryPreferred
* lightblue data service in datacenter B (secondary): nearest

By default the client will have fast responses.  If a client requires a consistent response the default read preference can be overriden by setting an [execution option](https://jewzaam.gitbooks.io/lightblue-specifications/content/language_specification/execution.html) on a lightblue request.  For example, for an application in datacenter B, a request to lightblue in datacenter B could be made with a "primary preferred" read preferency by:

```javascript
{
    ...
    "execution": {
        "readPreference": "primaryPreferred"
    }
}
```

### Default: Consistent Reads

The lightblue data services are configured with the following [read preferences](https://docs.mongodb.org/manual/core/read-preference/):
* lightblue data service in datacenter A (primary): primaryPreferred
* lightblue data service in datacenter B (secondary): primaryPreferred

By default the client will have consistent responses.  If a client requires a fast response the default read preference can be overriden by setting an [execution option](https://jewzaam.gitbooks.io/lightblue-specifications/content/language_specification/execution.html) on a lightblue request.  For example, for an application in datacenter B, a request to lightblue in datacenter B could be made with a "nearest" read preferency by:

```javascript
{
    ...
    "execution": {
        "readPreference": "nearest"
    }
}
```
