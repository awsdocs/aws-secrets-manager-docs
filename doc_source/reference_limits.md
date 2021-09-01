# AWS Secrets Manager quotas<a name="reference_limits"></a>

This section specifies quotas for AWS Secrets Manager\.

For information about **Service Endpoints**, see [AWS Secrets Manager endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/asm.html) which includes regional service endpoints\. You may operate multiple regions in your account, such as US East \(N\. Virginia\) Region and US West \(N\. California\) Region, and each quota is specific to each region\.

## Restrictions on names<a name="reference_limits_names"></a>

Secrets Manager has the following restrictions on names you create, including the names of secrets:
+ Secret names must use Unicode characters\.
+ Secrets names must not exceed 512 characters in length\.

## Maximum quotas<a name="reference_limits_max-min"></a>

You may operate multiple AWS Regions in your account, and each quota is specific to each AWS Region\.

The following are the maximum quotas for entities in AWS Secrets Manager:


****  

|  |  | 
| --- |--- |
| Entity | Value | 
| Secrets | 40,000 | 
| Versions of a secret | \~100  | 
| Labels attached across all versions of a secret | 20 | 
| Versions attached to a label at the same time | 1 | 
| Length of a secret | 65,536 bytes | 
| Length of a resource\-based policy \- JSON text | 20,480 characters  | 

## Rate quotas<a name="reference_limits_rates"></a>

These operations have the following rate quotas for AWS Secrets Manager:


****  

|  |  | 
| --- |--- |
| Request type | Number of operations permitted | 
|  DescribeSecret GetSecretValue  | 5,000 per second\. | 
|  CancelRotateSecret CreateSecret DeleteResourcePolicy DeleteSecret GetRandomPassword GetResourcePolicy ListSecrets ListSecretVersionIds PutResourcePolicy PutSecretValue RestoreSecret RotateSecret TagResource UntagResource UpdateSecret UpdateSecretVersionStage ValidateResourcePolicy  | 50 per second\. | 

We recommend you avoid calling `PutSecretValue` or `UpdateSecret` at a sustained rate of more than once every 10 minutes\. When you call `PutSecretValue` or `UpdateSecret` to update the secret value, Secrets Manager creates a new version of the secret\. Secrets Manager removes outdated versions when there are more than 100, but it does not remove versions created less than 24 hours ago\. If you update the secret value more than once every 10 minutes, you create more versions than Secrets Manager removes, and you will reach the quota for secret versions\.