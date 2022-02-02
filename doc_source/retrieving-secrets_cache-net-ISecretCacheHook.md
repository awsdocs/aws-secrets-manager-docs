# ISecretCacheHook<a name="retrieving-secrets_cache-net-ISecretCacheHook"></a>

An interface to hook into a [SecretsManagerCache](retrieving-secrets_cache-net-SecretsManagerCache.md) to perform actions on the secrets being stored in the cache\. 

## Methods<a name="retrieving-secrets_cache-net-ISecretCacheHook-methods"></a>

### Put<a name="retrieving-secrets_cache-net-ISecretCacheHook-methods-Put"></a>

`object Put(object o);`

Prepare the object for storing in the cache\.

Returns the object to store in the cache\.

### Get<a name="retrieving-secrets_cache-net-ISecretCacheHook-methods-Get"></a>

`object Get(object cachedObject);`

Derive the object from the cached object\.

Returns the object to return from the cache