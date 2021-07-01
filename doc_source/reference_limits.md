# AWS Secrets Manager quotas<a name="reference_limits"></a>

This section specifies quotas for AWS Secrets Manager\.

For information about **Service Endpoints**, see [AWS Secrets Manager endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/asm.html) which includes regional service endpoints\. You may operate multiple regions in your account, such as US East \(N\. Virginia\) Region and US West \(N\. California\) Region, and each quota is specific to each region\.

## Restrictions on names<a name="reference_limits_names"></a>

AWS Secrets Manager has the following restrictions on names you create, including the names of secrets:
+ Secret names must use Unicode characters\.
+ Secrets names must not exceed 512 characters in length\.

## Maximum quotas<a name="reference_limits_max-min"></a>

The following are the maximum quotas for entities in AWS Secrets Manager:

**Note**  
You may operate multiple regions in your account, and each quota is specific to each region\.


****  

|  |  | 
| --- |--- |
| Entity | Value | 
| Secrets in an AWS account | 40,000 | 
| Versions of a secret | \~100  | 
| Labels attached across all versions of a secret | 20 | 
| Versions attached to a label at the same time | 1 | 
| Length of a secret | 65,536 bytes | 
| Length of a resource\-based policy \- JSON text | 20,480 characters  | 

## Rate quotas<a name="reference_limits_rates"></a>

These parameters have the following rate quotas for AWS Secrets Manager:


****  

|  |  | 
| --- |--- |
| Request type | Number of operations permitted per second | 
|  DescribeSecret GetSecretValue  | 5,000 | 
|  PutResourcePolicy GetResourcePolicy DeleteResourcePolicy ValidateResourcePolicy CreateSecret UpdateSecret UpdateSecretVersionStage PutSecretValue DeleteSecret RestoreSecret RotateSecret CancelRotateSecret AssociateSecretLabels DisassociateSecretLabels GetRandomPassword TagResource UntagResource ListSecrets ListSecretVersionIds  | 50 | 