# @InjectKeywordedSecretString<a name="retrieving-secrets_cache-decor-keyword"></a>

This decorator expects a secret ID string and [SecretCache](retrieving-secrets_cache-ref-secretcache.md) as the first and second arguments\. The remaining arguments map parameters from the wrapped function to JSON keys in the secret\. The secret must contain a string in JSON structure\. 

For a secret that contains this JSON:

```
{
  "username": "saanvi",
  "password": "2J{X[Fif*Sp7L+jyDD0!-WH;&>\\hlgtr"
}
```

The following example shows how to extract the JSON values for `username` and `password` from the secret\.

```
from aws_secretsmanager_caching import SecretCache 
  from aws_secretsmanager_caching import InjectKeywordedSecretString,  InjectSecretString 
  
  cache = SecretCache()
  
  @InjectKeywordedSecretString ( secret_id = 'mysecret' ,  cache = cache ,  func_username = 'username' ,  func_password = 'password' ) def function_to_be_decorated( func_username,  func_password):
       print( 'Do something with the func_username and func_password parameters')
```