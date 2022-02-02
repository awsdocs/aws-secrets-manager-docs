# @InjectSecretString<a name="retrieving-secrets_cache-decor-string"></a>

This decorator expects a secret ID string and [SecretCache](retrieving-secrets_cache-ref-secretcache.md) as the first and second arguments\. The decorator returns the secret string value\. The secret must contain a string\. 

```
from aws_secretsmanager_caching import SecretCache 
from aws_secretsmanager_caching import InjectKeywordedSecretString,  InjectSecretString 

cache = SecretCache()

@InjectSecretString ( 'mysecret' ,  cache ) def function_to_be_decorated( arg1,  arg2,  arg3):
```