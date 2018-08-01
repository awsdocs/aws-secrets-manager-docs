# Limits of AWS Secrets Manager<a name="reference_limits"></a>

This section specifies limits that affect AWS Secrets Manager\.

## Limits on Names<a name="reference_limits_names"></a>

The following are restrictions on names that you create in AWS Secrets Manager \(including names of secrets\):
+ They must be composed of Unicode characters\.
+ They must not exceed 256 characters in length\.

## Maximum and Minimum Values<a name="reference_limits_max-min"></a>

The following are the default maximums for entities in AWS Secrets Manager:


****  

|  |  | 
| --- |--- |
| Max number of secrets in an AWS account | 40,000 | 
| Max number of versions in a secret | \~100  | 
| Max number of labels you can attach to a version | 20 | 
| Max number of versions a label can be attached to at the same time | 1 | 
| Maximum length of a secret | 4096 characters | 

## Maximum Rate Limits<a name="reference_limits_rates"></a>

The following are the rate limits for AWS Secrets Manager:


****  

|  |  | 
| --- |--- |
| Request type | Number per second \(bursts\) | 
| CreateSecret | 20 | 
|  UpdateSecret UpdateSecretVersionStage PutSecretValue  | 20 | 
| DeleteSecret | 20 | 
| RestoreSecret | 20 | 
| RotateSecret | 20 | 
| ListSecrets | 2 \(10\) | 
|  DescribeSecret GetSecretValue  | 100 \(500\) | 
|  TagResource UntagResource  | 10 | 
| GetRandomPassword | 5 | 