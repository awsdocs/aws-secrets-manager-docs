# SecretCacheHook<a name="retrieving-secrets_cache-java-ref_SecretCacheHook"></a>

An interface to hook into a [SecretCache](retrieving-secrets_cache-java-ref_SecretCache.md) to perform actions on the secrets being stored in the cache\. 

## put<a name="retrieving-secrets_cache-java-ref_SecretCacheHook-put"></a>

`Object put(final Object o)`

Prepare the object for storing in the cache\.

Returns the object to store in the cache\.

## get<a name="retrieving-secrets_cache-java-ref_SecretCacheHook-get"></a>

`Object get(final Object cachedObject)`

Derive the object from the cached object\.

Returns the object to return from the cache