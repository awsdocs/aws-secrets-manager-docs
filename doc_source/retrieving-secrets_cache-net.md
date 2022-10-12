# Retrieve AWS Secrets Manager secrets in \.NET applications<a name="retrieving-secrets_cache-net"></a>

When you retrieve a secret, you can use the Secrets Manager \.NET\-based caching component to cache it for future use\. Retrieving a cached secret is faster than retrieving it from Secrets Manager\. Because there is a cost for calling Secrets Manager APIs, using a cache can reduce your costs\. 

The cache policy is Least Recently Used \(LRU\), so when the cache must discard a secret, it discards the least recently used secret\. By default, the cache refreshes secrets every hour\. You can configure how often the secret is refreshed in the cache, and you can hook into the secret retrieval to add more functionality\.

To use the component, you must have the following:
+ \.NET Framework 4\.6\.1 or higher, or \.NET Standard 2\.0 or higher\. See [Download \.NET](https://dotnet.microsoft.com/en-us/download) on the Microsoft \.NET website\.
+ The AWS SDK for \.NET\. See [AWS SDKs](asm_access.md#asm-sdks)\.

To download the source code, see [Caching client for \.NET](https://github.com/aws/aws-secretsmanager-caching-net ) on GitHub\.

To use the cache, first instantiate it, then retrieve your secret by using `GetSecretString` or `GetSecretBinary`\. On successive retrievals, the cache returns the cached copy of the secret\.

To install the package from NuGet, using the dotnet CLI, enter the below command in the directory containing your project:

```
dotnet add package AWSSDK.SecretsManager.Caching --version 1.0.4
```

Or manually copy & add the below package reference to your `.csproj` file:

```
<ItemGroup>
    <PackageReference Include="AWSSDK.SecretsManager.Caching" Version="1.0.4" />
</ItemGroup>
```

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
        private const string MySecretName = "MySecret";

        private SecretsManagerCache cache = new SecretsManagerCache();

        public async Task<Response> FunctionHandlerAsync(string input, ILambdaContext context)
        {
            string mySecret = await cache.GetSecretString(MySecretName);

            // Use the secret, return success

        }
    }
}
```