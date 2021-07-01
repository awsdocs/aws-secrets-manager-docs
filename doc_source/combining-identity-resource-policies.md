# AWS managed policies<a name="combining-identity-resource-policies"></a>

AWS addresses many common use cases by providing standalone IAM policies created and administered by AWS\. These *managed policies* grant necessary permissions for common use cases so you can avoid investigating the necessary permissions\. For more information, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

Secrets Manager has a specific AWS managed policy, which you can attach to users in your account:
+ **[SecretsManagerReadWrite](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/SecretsManagerReadWrite)**–This policy can be attached to IAM users and roles to administer Secrets Manager\. The policy grants full permissions to the Secrets Manager service , and limited permissions to other services, such as AWS KMS, Amazon CloudFront, AWS Serverless Application Repository, and AWS Lambda\.

  You can view this policy in the [AWS Management Console](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/SecretsManagerReadWrite)\.
**Important**  
For security reasons, this managed policy does ***not*** include the IAM permissions needed to configure rotation\. You must explicitly grant permissions separately\. Typically, you attach the [IAMFullAccess](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/IAMFullAccess) managed policy to a Secrets Manager administrator to enable them to configure rotation\.

The Secrets Manager team maintains the AWS managed policy which can be an important advantage of using an AWS managed policy\. If the rotation process expands to include new functionality that requires additional permissions, Secrets Manager adds those permissions to the managed policy at that time\. Your rotation functions automatically receive the new permissions so that they continue to operate as expected without any interruption\.