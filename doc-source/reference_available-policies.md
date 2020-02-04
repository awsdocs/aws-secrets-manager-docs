# AWS Managed Policies Available for Use with AWS Secrets Manager<a name="reference_available-policies"></a>

This section identifies the AWS managed policies you can use to help manage access to your secrets\. You can't modify or delete an AWS managed policy, but you can attach or detach them to entities in your account as needed\.


****  

| Policy Name | Description | ARN | 
| --- | --- | --- | 
| [SecretsManagerReadWrite](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/SecretsManagerReadWrite) | Provides access to most Secrets Manager operations\. The policy doesn't enable configuring rotation because rotation requires IAM permissions to create roles\. For someone who must configure Lambda rotation functions and enable rotation, you should also assign the [IAMFullAccess](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/IAMFullAccess) managed policy\. | arn:aws:iam::aws:policy/SecretsManagerReadWrite | 