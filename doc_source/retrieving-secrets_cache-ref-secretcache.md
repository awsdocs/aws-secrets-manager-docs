# SecretCache<a name="retrieving-secrets_cache-ref-secretcache"></a>

An in\-memory cache for secrets retrieved from Secrets Manager\. You use [get\_secret\_string](#retrieving-secrets_cache-ref-secretcache_get_secret_string) or [get\_secret\_binary](#retrieving-secrets_cache-ref-secretcache_get_secret_binary) to retrieve a secret from the cache\. You can configure the cache settings by passing in a [SecretCacheConfig](retrieving-secrets_cache-ref-secretcacheconfig.md) object in the constructor\. 

For more information, including examples, see [Retrieve AWS Secrets Manager secrets in Python applications](retrieving-secrets_cache-python.md)\.

```
cache = SecretCache(
    config = SecretCacheConfig,
    client = [client](https://botocore.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html)
)
```

**Topics**
+ [get\_secret\_string](#retrieving-secrets_cache-ref-secretcache_get_secret_string)
+ [get\_secret\_binary](#retrieving-secrets_cache-ref-secretcache_get_secret_binary)

## get\_secret\_string<a name="retrieving-secrets_cache-ref-secretcache_get_secret_string"></a>

Retrieves the secret string value\.

Request syntax  

```
response = cache.get_secret_string(
    secret_id='string',
    version_stage='string' )
```

Parameters  
+ `secret_id` \(*string*\) \-\- \[Required\] The name or ARN of the secret\.
+ `version_stage` \(*string*\) \-\- The version of secrets that you want to retrieve\. For more information, see [Secret versions](https://docs.aws.amazon.com/secretsmanager/latest/userguide/getting-started.html#term_version)\. The default is 'AWSCURRENT'\. 

Return type  
string

## get\_secret\_binary<a name="retrieving-secrets_cache-ref-secretcache_get_secret_binary"></a>

Retrieves the secret binary value\.

Request syntax  

```
response = cache.get_secret_binary(
    secret_id='string',
    version_stage='string'
)
```

Parameters  
+ `secret_id` \(*string*\) \-\- \[Required\] The name or ARN of the secret\.
+ `version_stage` \(*string*\) \-\- The version of secrets that you want to retrieve\. For more information, see [Secret versions](https://docs.aws.amazon.com/secretsmanager/latest/userguide/getting-started.html#term_version)\. The default is 'AWSCURRENT'\. 

Return type  
[base64\-encoded](https://tools.ietf.org/html/rfc4648#section-4) string