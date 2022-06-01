# AWS Secrets Manager quotas<a name="reference_limits"></a>

This section specifies quotas for AWS Secrets Manager\. For Secrets Manager quotas for resources, see [Maximum quotas](#reference_limits_max-min)\. For Secrets Manager APIs, read APIs have high TPS quotas, and control plane APIs that are less frequently called have lower TPS quotas\. For more information, see [Rate quotas](#reference_limits_rates)\. 

For information about **Service Endpoints**, see [AWS Secrets Manager endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/asm.html) which includes regional service endpoints\. You may operate multiple regions in your account, such as US East \(N\. Virginia\) Region and US West \(N\. California\) Region, and each quota is specific to each region\.

## Secret name constraints<a name="reference_limits_names"></a>

Secrets Manager has the following constraints:
+ Secret names must use Unicode characters\.
+ Secret names contain 1\-512 characters\.

## Maximum quotas<a name="reference_limits_max-min"></a>

You can operate multiple AWS Regions in your account, and each quota is per AWS Region\.


****  

| Entity | Quota | 
| --- | --- | 
| Secrets | 500,000 | 
| Versions of a secret | \~100  | 
| Staging labels attached across all versions of a secret | 20 | 
| Versions attached to a label at the same time | 1 | 
| Length of a secret | 65,536 bytes | 
| Length of a resource\-based policy \- JSON text | 20,480 characters  | 

## Rate quotas<a name="reference_limits_rates"></a>

You can operate multiple AWS Regions in your account, and each quota is per AWS Region\.


****  

| Request type | Quota \(per second\) | 
| --- | --- | 
| `DescribeSecret` and  `GetSecretValue`, combined | 5,000 | 
| `CreateSecret` | 50  | 
| `DeleteSecret` | 50  | 
| `GetRandomPassword` | 50  | 
|  `ListSecrets` and  `ListSecretVersionIds`, combined  | 50 | 
|  `DeleteResourcePolicy`,  `GetResourcePolicy`, `PutResourcePolicy`, and  `ValidateResourcePolicy`, combined  | 50  | 
|  `PutSecretValue`, `RemoveRegionsFromReplication`, `ReplicateSecretToRegions`, `StopReplicationToReplica`, `UpdateSecret`, and  `UpdateSecretVersionStage`, combined  | 50  | 
| `RestoreSecret` | 50 | 
|  `RotateSecret` and  `CancelRotateSecret`, combined  | 50  | 
|  `TagResource` and  `UntagResource`, combined  | 50  | 

We recommend you avoid calling `PutSecretValue` or `UpdateSecret` at a sustained rate of more than once every 10 minutes\. When you call `PutSecretValue` or `UpdateSecret` to update the secret value, Secrets Manager creates a new version of the secret\. Secrets Manager removes outdated versions when there are more than 100, but it does not remove versions created less than 24 hours ago\. If you update the secret value more than once every 10 minutes, you create more versions than Secrets Manager removes, and you will reach the quota for secret versions\.

## Cross\-account requests<a name="reference_limits_cross-account"></a>

When an application in one AWS account uses a secret owned by a different account, it's known as a *cross\-account request*\. For cross\-account requests, Secrets Manager throttles the account of the identity that makes the requests, not the account that owns the secret\. For example, if an identity from account A uses a secret in account B, the secret use applies only to the quotas in account A\.