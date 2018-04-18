# AWS Managed Policies Available for Use with AWS Secrets Manager<a name="reference_available-policies"></a>

This section identifies the AWS managed policies provided to help you manage access to your secrets\. You cannot modify or delete an AWS managed policy, but you can attach or detach them to entities in your account as needed\.


****  

| Policy Name | Description | ARN | 
| --- | --- | --- | 
| [SecretsManagerReadWrite](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/SecretsManagerReadWrite) | Provides access to most Secrets Manager operations\. By itself, it does not enable configuring rotation because that also requires IAM permisions to create roles\. For someone who must configure Lambda rotation functions, you should also assign the [IAMFullAccess](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/IAMFullAccess) managed policy\. | arn:aws:iam::aws:policy/SecretsManagerReadWrite | 