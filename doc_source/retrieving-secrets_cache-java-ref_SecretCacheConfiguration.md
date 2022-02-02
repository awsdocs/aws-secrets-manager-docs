# SecretCacheConfiguration<a name="retrieving-secrets_cache-java-ref_SecretCacheConfiguration"></a>

Cache configuration options for a [SecretCache](retrieving-secrets_cache-java-ref_SecretCache.md), such as max cache size and Time to Live \(TTL\) for cached secrets\.

## Constructor<a name="retrieving-secrets_cache-java-ref_SecretCacheConfiguration_constructor"></a>

`public SecretCacheConfiguration`

Default constructor for a `SecretCacheConfiguration` object\.

## Methods<a name="retrieving-secrets_cache-java-ref_SecretCacheConfiguration_methods"></a>

### getClient<a name="retrieving-secrets_cache-java-ref_SecretCacheConfiguration_methods-getClient"></a>

`public AWSSecretsManager getClient()`

Returns the [https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/AWSSecretsManagerClient.html](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/AWSSecretsManagerClient.html) that the cache retrieves secrets from\.

### setClient<a name="retrieving-secrets_cache-java-ref_SecretCacheConfiguration_methods-setClient"></a>

`public void setClient(AWSSecretsManager client)`

Sets the [https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/AWSSecretsManagerClient.html](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/AWSSecretsManagerClient.html) client that the cache retrieves secrets from\.

### getCacheHook<a name="retrieving-secrets_cache-java-ref_SecretCacheConfiguration_methods-getCacheHook"></a>

`public SecretCacheHook getCacheHook()`

Returns the [SecretCacheHook](retrieving-secrets_cache-java-ref_SecretCacheHook.md) interface used to hook cache updates\.

### setCacheHook<a name="retrieving-secrets_cache-java-ref_SecretCacheConfiguration_methods-setCacheHook"></a>

`public void setCacheHook(SecretCacheHook cacheHook)`

Sets the [SecretCacheHook](retrieving-secrets_cache-java-ref_SecretCacheHook.md) interface used to hook cache updates\.

### getMaxCacheSize<a name="retrieving-secrets_cache-java-ref_SecretCacheConfiguration_methods-getMaxCacheSize"></a>

`public int getMaxCacheSize()`

Returns the maximum cache size\. The default is 1024 secrets\.

### setMaxCacheSize<a name="retrieving-secrets_cache-java-ref_SecretCacheConfiguration_methods-setMaxCacheSize"></a>

`public void setMaxCacheSize(int maxCacheSize)`

Sets the maximum cache size\. The default is 1024 secrets\.

### getCacheItemTTL<a name="retrieving-secrets_cache-java-ref_SecretCacheConfiguration_methods-getCacheItemTTL"></a>

`public long getCacheItemTTL()`

Returns the TTL in milliseconds for the cached items\. When a cached secret exceeds this TTL, the cache retrieves a new copy of the secret from the [https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/AWSSecretsManagerClient.html](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/AWSSecretsManagerClient.html)\. The default is 1 hour in milliseconds\. 

The cache refreshes the secret synchronously when the secret is requested after the TTL\. If the synchronous refresh fails, the cache returns the stale secret\. 

### setCacheItemTTL<a name="retrieving-secrets_cache-java-ref_SecretCacheConfiguration_methods-setCacheItemTTL"></a>

`public void setCacheItemTTL(long cacheItemTTL)`

Sets the TTL in milliseconds for the cached items\. When a cached secret exceeds this TTL, the cache retrieves a new copy of the secret from the [https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/AWSSecretsManagerClient.html](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/AWSSecretsManagerClient.html)\. The default is 1 hour in milliseconds\.

### getVersionStage<a name="retrieving-secrets_cache-java-ref_SecretCacheConfiguration_methods-getVersionStage"></a>

`public String getVersionStage()`

Returns the version of secrets that you want to cache\. For more information, see [Secret versions](getting-started.md#term_version)\. The default is` "AWSCURRENT"`\.

### setVersionStage<a name="retrieving-secrets_cache-java-ref_SecretCacheConfiguration_methods-setVersionStage"></a>

`public void setVersionStage(String versionStage)`

Sets the version of secrets that you want to cache\. For more information, see [Secret versions](getting-started.md#term_version)\. The default is `"AWSCURRENT"`\.

### SecretCacheConfiguration withClient<a name="retrieving-secrets_cache-java-ref_SecretCacheConfiguration_methods-withClient"></a>

`public SecretCacheConfiguration withClient(AWSSecretsManager client)`

Sets the [https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/AWSSecretsManagerClient.html](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/AWSSecretsManagerClient.html) to retrieve secrets from\. Returns the updated `SecretCacheConfiguration` object with the new setting\.

### SecretCacheConfiguration withCacheHook<a name="retrieving-secrets_cache-java-ref_SecretCacheConfiguration_methods-withCacheHook"></a>

`public SecretCacheConfiguration withCacheHook(SecretCacheHook cacheHook)`

Sets the interface used to hook the in\-memory cache\. Returns the updated `SecretCacheConfiguration` object with the new setting\.

### SecretCacheConfiguration withMaxCacheSize<a name="retrieving-secrets_cache-java-ref_SecretCacheConfiguration_methods-withMaxCacheSize"></a>

`public SecretCacheConfiguration withMaxCacheSize(int maxCacheSize)`

Sets the maximum cache size\. Returns the updated `SecretCacheConfiguration` object with the new setting\.

### SecretCacheConfiguration withCacheItemTTL<a name="retrieving-secrets_cache-java-ref_SecretCacheConfiguration_methods-withCacheItemTTL"></a>

`public SecretCacheConfiguration withCacheItemTTL(long cacheItemTTL)`

Sets the TTL in milliseconds for the cached items\. When a cached secret exceeds this TTL, the cache retrieves a new copy of the secret from the [https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/AWSSecretsManagerClient.html](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/AWSSecretsManagerClient.html)\. The default is 1 hour in milliseconds\. Returns the updated `SecretCacheConfiguration` object with the new setting\.

### SecretCacheConfiguration withVersionStage<a name="retrieving-secrets_cache-java-ref_SecretCacheConfiguration_methods-withVersionStage"></a>

`public SecretCacheConfiguration withVersionStage(String versionStage)`

Sets the version of secrets that you want to cache\. For more information, see [Secret versions](getting-started.md#term_version)\. Returns the updated `SecretCacheConfiguration` object with the new setting\.