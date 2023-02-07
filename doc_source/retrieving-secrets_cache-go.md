# Retrieve AWS Secrets Manager secrets in Go applications<a name="retrieving-secrets_cache-go"></a>

When you retrieve a secret, you can use the Secrets Manager Go\-based caching component to cache it for future use\. Retrieving a cached secret is faster than retrieving it from Secrets Manager\. Because there is a cost for calling Secrets Manager APIs, using a cache can reduce your costs\.

The cache policy is Least Recently Used \(LRU\), so when the cache must discard a secret, it discards the least recently used secret\. By default, the cache refreshes secrets every hour\. You can configure [how often the secret is refreshed](retrieving-secrets_cache-go_CacheConfig.md) in the cache, and you can [hook into the secret retrieval](retrieving-secrets_cache-go_CacheHook.md) to add more functionality\.

The cache does not force garbage collection once cache references are freed\. The cache implementation is focused around the cache itself, and is not security hardened or focused\. If you require additional security such as encrypting items in the cache, use the interfaces and abstract methods provided\.

To use the component, you must have the following:
+ AWS SDK for Go\. See [AWS SDKs](asm_access.md#asm-sdks)\.

To download the source code, see [Secrets Manager Go caching client](https://github.com/aws/aws-secretsmanager-caching-go ) on GitHub\.

To set up a Go development environment, see [Golang Getting Started](https://golang.org/doc/install) on the Go Programming Language website\.

**Required permissions: **
+ `secretsmanager:DescribeSecret`
+ `secretsmanager:GetSecretValue`

For more information, see [Permissions reference](reference_iam-permissions.md)\.

**Topics**
+ [type Cache](retrieving-secrets_cache-go_cache.md)
+ [type CacheConfig](retrieving-secrets_cache-go_CacheConfig.md)
+ [type CacheHook](retrieving-secrets_cache-go_CacheHook.md)

**Example: Retrieve a secret**  
The following code example shows a Lambda function that retrieves a secret\.  

```
package main

import (
	 "github.com/aws/aws-lambda-go/lambda"
	 "github.com/aws/aws-secretsmanager-caching-go/secretcache"
)

var (
	 secretCache, _ = secretcache.New()
)

func HandleRequest(secretId string) string {
	 result, _ := secretCache.GetSecretString(secretId)
	 
	 // Use the secret, return success
}

 func main() {
	 lambda. Start( HandleRequest)
}
```