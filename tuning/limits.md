# Limits

To make sure Lightblue does not run out of memory when processing large responses, define limits. NOTE: whenever available, use a streamed api. It scales well with response size and does not require any limits. 

## Max result set size in bytes

Lightblue accumulates results in memory before sending them to the client, which means a single request can easily lead to OutOfMemoryError. Updates can be more expensive than finds if you use hooks, because HookManager makes a copy of each document before it is modified.

### Configuration

To limit the allowed response size, define maxResultSetSizeForReadsB in lightblue-crud.json. To limit the allowed response size for updates (including any copies created by HookManager), define maxResultSetSizeForWritesB in lightblue-crud.json. Set warnResultSetSizeB to start logging warnings whenever response size exceeds the threshold. By default, maxResultSetSizeForReadsB and maxResultSetSizeForWritesB limits are not enforced.

### Client impact

When client exceeds the read limit (maxResultSetSizeForReadsB), an error is returned: _crud:ResultSizeTooLarge_. No results are returned.

When client exceeds the write limit (maxResultSetSizeForWritesB), either _crud:ResultSizeTooLarge_ or _mongo-crud:ResultSizeTooLarge_ is returned. No results are returned, **however, the update operation was partially successful. You cannot make any assumptions on what was or wasn't updated.**. Lightblue processes updates in batches of 64. It will process all batches up to a point where it exceeded the limit and stop, leaving all remaining batches unprocessed and skipping hook processing phase (if there are any hooks defined), potentially leading to more inconsistency.

Considering the above, set maxResultSetSizeForWritesB as high as possible without compromising the availability of the environment. 
