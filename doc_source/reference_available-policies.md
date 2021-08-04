# AWS managed policies available for use with AWS Secrets Manager<a name="reference_available-policies"></a>

AWS addresses many common use cases by providing standalone IAM policies created and administered by AWS\. These *managed policies* grant necessary permissions for common use cases so you can avoid investigating the necessary permissions\. For more information, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

This section identifies the AWS managed policies you can use to help manage access to your secrets\. You can attach or detach an AWS managed policy to users in your account, but you can't modify or delete the policy\.


****  

| Policy Name | Description | ARN | 
| --- | --- | --- | 
| [SecretsManagerReadWrite](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/SecretsManagerReadWrite) | Provides access to most Secrets Manager operations\. The policy doesn't enable configuring rotation because rotation requires IAM permissions to create roles\. For someone who must configure Lambda rotation functions and enable rotation, you should also assign the [IAMFullAccess](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/IAMFullAccess) managed policy\. | arn:aws:iam::aws:policy/SecretsManagerReadWrite | 