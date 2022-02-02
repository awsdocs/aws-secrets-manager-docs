# type CacheHook<a name="retrieving-secrets_cache-go_CacheHook"></a>

An interface to hook into a [Cache](retrieving-secrets_cache-go_cache.md) to perform actions on the secret being stored in the cache\.

## Methods<a name="retrieving-secrets_cache-go_CacheHook_operations"></a>

### Put<a name="retrieving-secrets_cache-go_CacheHook_operations_Put"></a>

`Put(data interface{}) interface{}`

Prepares the object for storing in the cache\.

### Get<a name="retrieving-secrets_cache-go_CacheHook_operations_Get"></a>

`Get(data interface{}) interface{}`

Derives the object from the cached object\.