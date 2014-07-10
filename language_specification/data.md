# Data

## Data APIs

All Data APIs get a Json request object, and return a Json response
object.

### Common: Request

```
{
  entity: <entityName>,
  entityVersion: <entity metadata version>,
  client: <clientIdentification>,
  execution: <executionOptions>
}
```

* entity: Mandatory field that specifies the name of the
  entity to be operated on
* entityVersion: Optional field that gives the version of
  metadata. If not given, latest version is assumed.
* clientId: Optional field. This field contains the authentication
  information for the caller, which is auth implementation
  specific, or a session identifier obtained from an earlier call.
* execution: Optional execution flags. If omitted, the call
  is sycnchronous, and times out with partial completion if
  execution lasts longer than a preset limit.
  * timeLimit: The upper time limit for the call. If the call does
    not complete before the limit expires, call fails with partial
    results written. The return status will contain what items are
    written.
  * asynchronous: Given as a time limit. Once that limit is exceeded,
    the call returns, but execution continues. The return value will
    contain a task handle that can be used to check for execution status.


### Common: Response

```
{
  status: one of complete, partial, async, or error
  modifiedCount: int
  matchCount: int
  taskHandle: sync
  session:<sessionInfo>
  processed: [ <entity data> ]
  dataErrors: [ { data: <entity data>, errors: [ <errors> ] } ]
  errors: [ <errors> ]
}
```
* status: enumerated field
  * complete: The operation completed, all requests are processed
  * partial: Some of the operations are successful, some failed
  * async: Operation running asynchronously
  * error: Operation failed because of some system error
* modifiedCount: number of entities affected by the call,
  if this is an insert, update, or delete
* matchCount: total number of entities in the result set,
  if this is a find.
* taskHandle: Only present if aysnchronous operation is
  requested, and status=async. Contains the task handle that
  the client can use to retrieve status information about the
  ongoing execution
* session: Session information for the client. How this is
  used is implementation dependent, and TBD
* processed: Projected entity information for processed entities.
  Only for insert and update. Contains only successfully updated
  entities. It is up to the caller to project identifying fields
  if caller needs.
* dataErrors: All entity related errors.
  * data: Projected entity data
  * errors: Array of errors for that particular object
* errors: Errors not related to data


### Insert

Request object:
```
{
  <common stuff>
  data: [ <entityData> ]
  returning : <projection>
}
```
* data: Array of entity objects
  * If object ids are given, entities will be inserted
    with the given object id, otherwise object id will be auto-generated
* returning: A projection expression specifying what fields of entities
  to return once the insertion is performed.

### Save

Updates or inserts entities with the given ID.

Request object:
```
{
  <common stuff>
  data: [ <entityData> ],
  upsert: boolean
  returning : <projection>
}
```
* data: Array of entity objects
  * Objects IDs must be given for objects to be updated
  * If an object with the same ID exists, that object is updated.
    Otherwise, if upsert is true, that object is inserted, if not,
    error is returned.
* upsert: If true, inserts the doc if not found, or if the doc
  doesn't have id
* returning: A projection expression specifying what fields of updated
  entities to return

### Update

Partially updates entities matching a search criteria

Request object:
```
{
  <common stuff>,
  query: <query_expression,
  update: <update_expression>
  returning : <projection>
}
```
* query: A query expression determining which documents to update
* update: update expression
* returning: A projection expression specifying what fields of updated
  entities to return

### Delete

Request object:
```
{
  <common stuff>
  query: <query_expression>,
}
```

### Find

Request object:
```
{
  <common stuff>
  query: <query_expression>,
  returning: <projection>,
  sort: <sort>,
  range: [ from, to ]
}
```
* query: A non-empty query expression
* returning: A non-empty projection expression specifying what to return
* sort: Optional sort specification
* range: Optional range, inclusive range of results to return.
  If sort is not given, the results will be returned in an
  unspecified order, making range useless. There is no guarantee
  that subsequent calls will retrieve the results in the same order.

