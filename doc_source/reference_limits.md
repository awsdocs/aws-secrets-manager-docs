# Quotas for AWS Secrets Manager<a name="reference_limits"></a>

This section specifies quotas for AWS Secrets Manager\.

## Restrictions on Names<a name="reference_limits_names"></a>

AWS Secrets Manager has the following restrictions on names you create, including the names of secrets:
+ Secret names must use Unicode characters\.
+ Secrets names must not exceed 512 characters in length\.

## Maximum Quotas<a name="reference_limits_max-min"></a>

The following are the maximum quotas for entities in AWS Secrets Manager:


****  

|  |  | 
| --- |--- |
| Entity | Value | 
| Secrets in an AWS account | 40,000 | 
| Versions of a secret | \~100  | 
| Labels attached across all versions of a secret | 20 | 
| Versions attached to a label at the same time | 1 | 
| Length of a secret |  65,536 bytes | 
| Length of a resource\-based policy \- JSON text |  20,480 characters  | 

## Rate Quotas<a name="reference_limits_rates"></a>

These parameters have the following rate quotas for AWS Secrets Manager:


****  

|  |  | 
| --- |--- |
| Request type | Number of operations permitted per second | 
| Overall API quota for the account | 2,000 | 
|  DescribeSecret GetSecretValue  | 2,000 | 
|  PutResourcePolicy GetResourcePolicy DeleteResourcePolicy CreateSecret UpdateSecret UpdateSecretVersionStage PutSecretValue DeleteSecret RestoreSecret RotateSecret CancelRotateSecret AssociateSecretLabels DisassociateSecretLabels GetRandomPassword  | 40 | 
|  TagResource UntagResource ListSecrets ListSecretVersionIds  | 20 | 