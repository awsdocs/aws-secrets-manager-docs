# Authentication and access control for AWS Secrets Manager<a name="auth-and-access"></a>

Secrets Manager uses [AWS Identity and Access Management \(IAM\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) to secure access to secrets\. IAM provides authentication and access control\. *Authentication* verifies the identity of individuals' requests\. Secrets Manager uses a sign\-in process with passwords, access keys, and multi\-factor authentication \(MFA\) tokens to verify the identity of the users\. See [Signing in to AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/console.html)\. *Access control* ensures that only approved individuals can perform operations on AWS resources such as secrets\. Secrets Manager uses policies to define who has access to which resources, and which actions the identity can take on those resources\. See [Policies and permissions in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)\.

## Secrets Manager administrator permissions<a name="auth-and-access_admin"></a>

To grant Secrets Manager administrator permissions, follow the instructions at [Adding and removing IAM identity permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html), and attach the following policies:
+ [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_available-policies.html](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_available-policies.html)
+ [https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies)

We recommend you do not grant administrator permissions to end users\. While this allows your users to create and manage their secrets, the permission required to enable rotation \(IAMFullAccess\) grants significant permissions that are not appropriate for end users\.

## Permissions to access secrets<a name="auth-and-access_secrets"></a>

By using IAM permission policies, you control which users or services have access to your secrets\. A *permissions policy* describes who can perform which actions on which resources\. You can: 
+ [Attach a permissions policy to an identity](auth-and-access_iam-policies.md)
+ [Attach a permissions policy to a secret](auth-and-access_resource-policies.md)

## Permissions for Lambda rotation functions<a name="auth-and-access_rotate"></a>

Secrets Manager uses AWS Lambda functions to [rotate secrets](https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotating-secrets.html)\. The Lambda function must have access to the secret as well as the database or service that the secret contains credentials for\. See [Permissions for rotation](rotating-secrets-required-permissions-function.md)\.

## Permissions for encryption keys<a name="auth-and-access_encrypt"></a>

Secrets Manager uses AWS Key Management Service \(AWS KMS\) keys to [encrypt secrets](https://docs.aws.amazon.com/secretsmanager/latest/userguide/security-encryption.html)\. The AWS managed key `aws/secretsmanager` automatically has the correct permissions\. If you use a different KMS key, Secrets Manager needs permissions to that key\. See [Permissions for the KMS key](security-encryption.md#security-encryption-authz)\. 