# SecretCache<a name="retrieving-secrets_cache-java-ref_SecretCache"></a>

An in\-memory cache for secrets requested from Secrets Manager\. You use [getSecretString](#retrieving-secrets_cache-java-ref_SecretCache-methods-getSecretString) or [getSecretBinary](#retrieving-secrets_cache-java-ref_SecretCache-methods-getSecretBinary) to retrieve a secret from the cache\. You can configure the cache settings by passing in a [SecretCacheConfiguration](retrieving-secrets_cache-java-ref_SecretCacheConfiguration.md) object in the constructor\. 

For more information, including examples, see [Retrieve AWS Secrets Manager secrets in Java applications](retrieving-secrets_cache-java.md)\.

## Constructors<a name="retrieving-secrets_cache-java-ref_SecretCache-constructors"></a>

`public SecretCache()`  
Default constructor for a `SecretCache` object\.

`public SecretCache(AWSSecretsManagerClientBuilder builder)`  
Constructs a new cache using a Secrets Manager client created using the provided [https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/AWSSecretsManagerClientBuilder.html](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/AWSSecretsManagerClientBuilder.html)\. Use this constructor to customize the Secrets Manager client, for example to use a specific region or endpoint\.

`public SecretCache(AWSSecretsManager client)`  
Constructs a new secret cache using the provided [https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/AWSSecretsManagerClient.html](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/AWSSecretsManagerClient.html)\. Use this constructor to customize the Secrets Manager client, for example to use a specific region or endpoint\.

`public SecretCache(SecretCacheConfiguration config)`  
Constructs a new secret cache using the provided `SecretCacheConfiguration`\.

## Methods<a name="retrieving-secrets_cache-java-ref_SecretCache-methods"></a>

### getSecretString<a name="retrieving-secrets_cache-java-ref_SecretCache-methods-getSecretString"></a>

`public String getSecretString(final String secretId)`

Retrieves a string secret from Secrets Manager\. Returns a [https://docs.oracle.com/javase/7/docs/api/java/lang/String.html?is-external=true](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html?is-external=true)\.

### getSecretBinary<a name="retrieving-secrets_cache-java-ref_SecretCache-methods-getSecretBinary"></a>

`public ByteBuffer getSecretBinary(final String secretId)`

Retrieves a binary secret from Secrets Manager\. Returns a [https://docs.oracle.com/javase/7/docs/api/java/nio/ByteBuffer.html](https://docs.oracle.com/javase/7/docs/api/java/nio/ByteBuffer.html)\.

### refreshNow<a name="retrieving-secrets_cache-java-ref_SecretCache-methods-refreshNow"></a>

`public boolean refreshNow(final String secretId) throws InterruptedException`

Forces the cache to refresh\. Returns `true` if the refresh completed without error, otherwise `false`\.

### close<a name="retrieving-secrets_cache-java-ref_SecretCache-methods-close"></a>

`public void close()`

Closes the cache\.