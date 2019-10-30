# Limits for AWS Secrets Manager<a name="reference_limits"></a>

This section specifies limits for AWS Secrets Manager\.

## Restrictions on Names<a name="reference_limits_names"></a>

AWS Secrets Manager has the following restrictions on names you create, including the names of secrets:
+ Secrets must use Unicode characters\.
+ Secrets must not exceed 256 characters in length\.

## Maximum Limits<a name="reference_limits_max-min"></a>

The following are the default maximum limits for entities in AWS Secrets Manager:


****  

|  |  | 
| --- |--- |
| Maximum number of secrets in an AWS account | 40,000 | 
| Maximum number of versions in a secret | \~100  | 
| Maximum number of labels attached across all versions of a secret | 20 | 
| Maximum number of versions you can attach to a label at the same time | 1 | 
| Maximum length of a secret |  10240 bytes | 
| Maximum length of a resource\-based policy \- JSON text |  20480 characters  | 

## Maximum Rate Limits<a name="reference_limits_rates"></a>

These parameters have the following rate limits for AWS Secrets Manager:


****  

|  |  | 
| --- |--- |
| Request type | Number of operations permitted per second | 
| Overall API limit for the account | 1500 | 
|  DescribeSecret GetSecretValue  | 1500 | 
|  PutResourcePolicy GetResourcePolicy DeleteResourcePolicy CreateSecret UpdateSecret UpdateSecretVersionStage PutSecretValue DeleteSecret RestoreSecret RotateSecret CancelRotateSecret AssociateSecretLabels DisassociateSecretLabels GetRandomPassword  | 40 | 
|  TagResource UntagResource ListSecrets ListSecretVersionIds  | 20 | 