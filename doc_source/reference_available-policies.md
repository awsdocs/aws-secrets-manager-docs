# AWS managed policies available for use with AWS Secrets Manager<a name="reference_available-policies"></a>

AWS addresses many common use cases by providing *managed policies*, standalone IAM policies created and administered by AWS\. Managed policies grant permissions for common use cases so you can avoid investigating the necessary permissions\. You can attach or remove an AWS managed policy to users in your account, but you can't modify or delete the policy\. For more information, see [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

The following table describes the AWS managed policy you can use to help manage access to Secrets Manager secrets\. 


****  

| Policy Name | Description | ARN | 
| --- | --- | --- | 
| [SecretsManagerReadWrite](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/SecretsManagerReadWrite) | Provides access to Secrets Manager operations\. The policy doesn't allow the identity to configure rotation because rotation requires IAM permissions to create roles\. If you need to enable rotation and configure Lambda rotation functions, you need to also assign the [IAMFullAccess](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/IAMFullAccess) managed policy\. See [Permissions for the Lambda rotation function](rotating-secrets-required-permissions-function.md)\. | arn:aws:iam::aws:policy/SecretsManagerReadWrite | 