# Getting started with AWS Secrets Manager<a name="getting-started"></a>

The following concepts are important for understanding how Secrets Manager works\.

## Secret<a name="term_secret"></a>

In Secrets Manager, a *secret* consists of a set of credentials, user name and password, and the connection details to access a database or other service\. You can also store any other type of secret information in a secret as text or binary\. To store multiple values in one secret, we recommend that you use a JSON text string with key/value pairs, for example:

```
{
  "host"       : "ProdServer-01.databases.example.com",
  "port"       : "8888",
  "username"   : "administrator",
  "password"   : "My-P@ssw0rd!F0r+Th3_Acc0unt",
  "dbname"     : "MyDatabase",
  "engine"     : "mysql"
}
```

A secret has metadata:
+ The name of the secret, a description, an Amazon Resource Name \(ARN\), a resource policy, and tags\. 
+ The ARN for an *encryption key*, an AWS KMS key that Secrets Manager uses to encrypt and decrypt the secret value\. Secrets Manager stores secret text in an encrypted form and encrypts the secret in transit\. See [Encrypting and decrypting secrets](security-encryption.md)\.
+ Information about how to rotate the secret, if you set up rotation\. See [Rotation](#term_rotation)\.

Secrets Manager uses IAM permission policies to make sure that only authorized users can access or modify a secret\. See [Authentication and access control for AWS Secrets Manager](auth-and-access.md)\.

A secret has *versions* which hold copies of the encrypted secret value\. When you change the secret value, or the secret is rotated, Secrets Manager creates a new version\. See [Version](#term_version)\.

You can use a secret across multiple AWS Regions by *replicating* it\. When you replicate a secret, you create a copy of the original or *primary secret* called a *replica secret*\. The replica secret remains linked to the primary secret\. See [Creating and managing multi\-Region Secrets Manager secrets ](create-manage-multi-region-secrets.md)\.

To create a secret, see [Creating a secret](manage_create-basic-secret.md)\.

## Rotation<a name="term_rotation"></a>

*Rotation* is the process of periodically updating a secret to make it more difficult for an attacker to access the credentials\. In Secrets Manager, you can set up automatic rotation for your secrets\. When Secrets Manager rotates a secret, it updates the credentials in both the secret and the database or service\. See [Rotating secrets](rotating-secrets.md)\.

To rotate a secret, Secrets Manager uses a Lambda function called a *rotation function*\. See [Lambda rotation function](rotating-secrets-lambda-function-overview.md)\.

## Version<a name="term_version"></a>

A secret has *versions* which hold copies of the encrypted secret value\. When you change the secret value, or the secret is rotated, Secrets Manager creates a new version\. A secret always has a version with the staging label `AWSCURRENT`, which is the current secret value\. See [Staging label](#term_staging-label)\. 

Secrets Manager deprecates versions with no staging labels and removes them when there are more than 100\. Secrets Manager doesn't remove versions created less than 24 hours ago\.

When you use the AWS CLI or AWS SDK to get the secret value, you can specify the version of the secret\. If you don't specify a version, either by version ID or staging label, Secrets Manager gets the version with the staging label `AWSCURRENT` attached\.

## Staging label<a name="term_staging-label"></a>

Secrets Manager uses *staging labels* to identify different versions of a secret\. A secret always has a version with the staging label `AWSCURRENT`\. Secrets Manager uses the staging labels `AWSPENDING`, `AWSCURRENT`, and `AWSPREVIOUS` during rotation\. See [The steps of rotation](rotating-secrets-lambda-function-overview.md#rotation-explanation-of-steps)\. 

You can attach up to 20 staging labels to a secret\. Two versions of a secret can't have the same staging label\. 