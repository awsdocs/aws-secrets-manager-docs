# Retrieve AWS Secrets Manager secrets in \.NET applications<a name="retrieving-secrets_cache-net"></a>

When you retrieve a secret, you can use the Secrets Manager \.NET\-based caching component to cache it for future use\. Retrieving a cached secret is faster than retrieving it from Secrets Manager\. Because there is a cost for calling Secrets Manager APIs, using a cache can reduce your costs\.

The cache policy is Least Recently Used \(LRU\), so when the cache must discard a secret, it discards the least recently used secret\. By default, the cache refreshes secrets every hour\. You can configure [how often the secret is refreshed](retrieving-secrets_cache-net-SecretCacheConfiguration.md#retrieving-secrets_cache-net-SecretCacheConfiguration-properties_CacheItemTTL) in the cache, and you can [hook into the secret retrieval](retrieving-secrets_cache-net-ISecretCacheHook.md) to add more functionality\.

The cache does not force garbage collection once cache references are freed\. The cache implementation is focused around the cache itself, and is not security hardened or focused\. If you require additional security such as encrypting items in the cache, use the interfaces and abstract methods provided\.

To use the component, you must have the following:
+ \.NET Framework 4\.6\.1 or higher, or \.NET Standard 2\.0 or higher\. See [Download \.NET](https://dotnet.microsoft.com/en-us/download) on the Microsoft \.NET website\.
+ The AWS SDK for \.NET\. See [AWS SDKs](asm_access.md#asm-sdks)\.

To download the source code, see [Caching client for \.NET](https://github.com/aws/aws-secretsmanager-caching-net ) on GitHub\.

To use the cache, first instantiate it, then retrieve your secret by using `GetSecretString` or `GetSecretBinary`\. On successive retrievals, the cache returns the cached copy of the secret\.

**To get the caching package**
+ Do one of the following:
  + Run the following \.NET CLI command in your project directory\.

    ```
    dotnet add package AWSSDK.SecretsManager.Caching --version 1.0.4
    ```
  + Add the following package reference to your `.csproj` file\.

    ```
    <ItemGroup>
        <PackageReference Include="AWSSDK.SecretsManager.Caching" Version="1.0.4" />
    </ItemGroup>
    ```

**Required permissions: **
+ `secretsmanager:DescribeSecret`
+ `secretsmanager:GetSecretValue`

For more information, see [Permissions reference](reference_iam-permissions.md)\.

**Topics**
+ [SecretsManagerCache](retrieving-secrets_cache-net-SecretsManagerCache.md)
+ [SecretCacheConfiguration](retrieving-secrets_cache-net-SecretCacheConfiguration.md)
+ [ISecretCacheHook](retrieving-secrets_cache-net-ISecretCacheHook.md)

**Example: Retrieve a secret**  
The following code example shows a method that retrieves a secret named *MySecret*\.  

```
using Amazon.SecretsManager.Extensions.Caching;

namespace LambdaExample 
{
    public class CachingExample 
    {
        private const string MySecretName ="MySecret";

        private SecretsManagerCache cache = new SecretsManagerCache();

        public async Task<Response>  FunctionHandlerAsync(string input, ILambdaContext context)
        {
            string MySecret = await cache.GetSecretString(MySecretName);
            
            // Use the secret, return success
            
        }
    }
}
```

**Example: Configure the time to live \(TTL\) cache refresh duration**  
The following code example shows a method that retrieves a secret named *MySecret* and sets the TTL cache refresh duration to 24 hours\.  

```
using Amazon.SecretsManager.Extensions.Caching;

namespace LambdaExample
{
    public class CachingExample
    {
        private const string MySecretName = "MySecret";
        
        private static SecretCacheConfiguration cacheConfiguration = new SecretCacheConfiguration
        {
            CacheItemTTL = 86400000
        };
        private SecretsManagerCache cache = new SecretsManagerCache(cacheConfiguration);
        public async Task<Response> FunctionHandlerAsync(string input, ILambdaContext context)
        {
            string mySecret = await cache.GetSecretString(MySecretName);

            // Use the secret, return success
        }
    }
}
```