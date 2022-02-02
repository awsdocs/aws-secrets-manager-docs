# SecretCacheHook<a name="retrieving-secrets_cache-ref-secretcachehook"></a>

An interface to hook into a a [SecretCache](retrieving-secrets_cache-ref-secretcache.md) to perform actions on the secrets being stored in the cache\. 

**Topics**
+ [put](#retrieving-secrets_cache-ref-secretcachehook_put)
+ [get](#retrieving-secrets_cache-ref-secretcachehook_get)

## put<a name="retrieving-secrets_cache-ref-secretcachehook_put"></a>

Prepares the object for storing in the cache\.

Request syntax  

```
response = hook.put(
    obj='secret_object'
)
```

Parameters  
+ `obj` \(*object*\) \-\- \[Required\] The secret or object that contains the secret\.

Return type  
object

## get<a name="retrieving-secrets_cache-ref-secretcachehook_get"></a>

Derives the object from the cached object\.

Request syntax  

```
response = hook.get(
    obj='secret_object'
)
```

Parameters  
+ `obj` \(*object*\) \-\- \[Required\] The secret or object that contains the secret\.

Return type  
object