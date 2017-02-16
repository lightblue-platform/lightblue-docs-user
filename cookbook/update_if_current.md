# Update-if-current

Update-if-current feature is an optimistic locking implementation that
prevents unintended data overwrites. When results are returned from a
find operation, a document version string is also returned in result
metadata. The document version string is an opaque structure that
identifies a particular version of a document in an implementation
defined way. If the application performs modifications on data
retrieved from a find operation, it can specify 'onlyIfCurrent' flag,
and provide the version numner returned by the find operation. Then,
the save/update operationwill only update the document if the document
is not modified since the find operation is run.

Using JSON request/responses as an example:

Find request:
```
{
  "query": { "field":"_id","op":"=","rvalue":1 },
  "projection":{"field":"*"}
}
```

Find response:
```
{
  "processed": [ {...} ],
  "resultMetadata": [ { "documentVersion":"1:123" } ]
}
```

In the find response, the `resultMetadata` array contains document
versions for the document in `processed` array with the matching
index.

When the application want to update the data using a save operation:

Save request:
```
{
  "entityData": { ... },
  "documentVersions": [ "1:123" ],
  "onlyIfCurrent":true
}
```

In the save request, the document versions don't have to match the
entity data. The client provides the set of document versions it wants
to update, and only those documents will be updated if their versions
are unchanged. For instance, with an update request:

```
{
   "query":{"field":"someField","op":">","rvalue":100},
   "update":{"$set": { "otherField":500 } },
   "documentVersions": [ "1:123", "2:234" ],
   "onlyIfCurrent":true
}
```

Here, the update criteria will probably match many documents. However,
the documentVersions array specified only two versions. Therefore, the
update operation will attempt to update only two documents (those that
are given in the documentVersions array), and that only if those
documents match the update criteria. The documents will be updated
only if they still have the same versions given in the
documentVersions array.

## Lightblue client

This feature is accessible through the lightblue java client.

```
   DataFindRequest request=new DataFindRequest("entityName");
   request.where(Query.withValue("_id",Query.eq,1);
   request.select(Projection.includeFieldRecursively("*:));
   LightblueDataResponse response=cli.data(request);
   Entity e=response.parseProcessed(Entity.class);
   ResultMetadata[] metadata=response.getResultMetadata();
   System.out.println("Document version is:"+metadata[0].getDocumentVersion());
```

And for update:

```
   DataSaveRequest request=new DataSaveRequest("entityName");
   request.create(e);
   // Set document versions
   request.setIfCurrent(true);
   request.addDocumentVersions(metadata);
   cli.data(request);
```

Or:

```
   DataSaveRequest request=new DataSaveRequest("entityName");
   request.create(e);
   // Set document versions
   request.ifCurrent(metadata[0].getDocumentVersion);
   cli.data(request);
```
If the update fails, the server will return concurrent update error.
