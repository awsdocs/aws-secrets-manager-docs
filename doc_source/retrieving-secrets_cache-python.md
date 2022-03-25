# Retrieve AWS Secrets Manager secrets in Python applications<a name="retrieving-secrets_cache-python"></a>

When you retrieve a secret, you can use the Secrets Manager Python\-based caching component to cache it for future use\. Retrieving a cached secret is faster than retrieving it from Secrets Manager\. Because there is a cost for calling Secrets Manager APIs, using a cache can reduce your costs\. 

The cache policy is Least Recently Used \(LRU\), so when the cache must discard a secret, it discards the least recently used secret\. By default, the cache refreshes secrets every hour\. You can configure how often the secret is refreshed in the cache, and you can hook into the secret retrieval to add more functionality\.

To use the component, you must have the following: 
+ Python 3\.6 or later\.
+ botocore 1\.12 or higher\. See [AWS SDK for Python](https://aws.amazon.com/sdk-for-python/) and [Botocore](https://botocore.amazonaws.com/v1/documentation/api/latest/index.html)\. 
+ setuptools\_scm 3\.2 or higher\. See [https://pypi\.org/project/setuptools\-scm/](https://pypi.org/project/setuptools-scm/)\.

To download the source code, see [Secrets Manager Python\-based caching client component](https://github.com/aws/aws-secretsmanager-caching-python ) on GitHub\.

To install the component, use the following command\.

```
$ pip install aws-secretsmanager-caching
```

**Topics**
+ [SecretCache](retrieving-secrets_cache-ref-secretcache.md)
+ [SecretCacheConfig](retrieving-secrets_cache-ref-secretcacheconfig.md)
+ [SecretCacheHook](retrieving-secrets_cache-ref-secretcachehook.md)
+ [@InjectSecretString](retrieving-secrets_cache-decor-string.md)
+ [@InjectKeywordedSecretString](retrieving-secrets_cache-decor-keyword.md)

**Example: Retrieve a secret**  
The following example shows how to get the secret value for a secret named *mysecret*\.  

```
import botocore 
import botocore.session 
from aws_secretsmanager_caching import SecretCache, SecretCacheConfig 

client = botocore.session.get_session().create_client('secretsmanager')
cache_config = SecretCacheConfig()
cache = SecretCache( config = cache_config, client = client)

secret = cache.get_secret_string('mysecret')
```