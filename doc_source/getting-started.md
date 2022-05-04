# Get started with AWS Secrets Manager<a name="getting-started"></a>

There are many different types of secrets you might have in your organization\. Here are some of them, and where you can store them in AWS:
+ AWS credentials – [AWS Identity and Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)
+ Encryption keys – [AWS Key Management Service](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html)
+ SSH keys – [Amazon EC2 Instance Connect](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Connect-using-EC2-Instance-Connect.html)
+ Private keys and certificates – [AWS Certificate Manager](https://docs.aws.amazon.com/acm/latest/userguide/acm-overview.html)
+ **Database credentials – Secrets Manager**
+ **Application credentials – Secrets Manager**
+ **OAuth tokens – Secrets Manager**
+ **Application Programming Interface \(API\) keys – Secrets Manager**

## Secrets Manager concepts<a name="getting-started_concepts"></a>

The following concepts are important for understanding how Secrets Manager works\.

### Secret<a name="term_secret"></a>

In Secrets Manager, a *secret* consists of secret information, the *secret value*, plus metadata about the secret\. A secret value can be a string or binary\. To store multiple string values in one secret, we recommend that you use a JSON text string with key/value pairs, for example:

```
{
  "host"       : "ProdServer-01.databases.example.com",
  "port"       : "8888",
  "username"   : "administrator",
  "password"   : "EXAMPLE-PASSWORD",
  "dbname"     : "MyDatabase",
  "engine"     : "mysql"
}
```

A secret's metadata includes:
+ An Amazon Resource Name \(ARN\) with the following format:

  ```
  arn:aws:secretsmanager:<Region>:<AccountId>:secret:SecretName-6RandomCharacters
  ```
+ The name of the secret, a description, a resource policy, and tags\.
+ The ARN for an *encryption key*, an AWS KMS key that Secrets Manager uses to encrypt and decrypt the secret value\. Secrets Manager stores secret text in an encrypted form and encrypts the secret in transit\. See [Secret encryption and decryption](security-encryption.md)\.
+ Information about how to rotate the secret, if you set up rotation\. See [Rotation](#term_rotation)\.

Secrets Manager uses IAM permission policies to make sure that only authorized users can access or modify a secret\. See [Authentication and access control for AWS Secrets Manager](auth-and-access.md)\.

A secret has *versions* which hold copies of the encrypted secret value\. When you change the secret value, or the secret is rotated, Secrets Manager creates a new version\. See [Version](#term_version)\.

You can use a secret across multiple AWS Regions by *replicating* it\. When you replicate a secret, you create a copy of the original or *primary secret* called a *replica secret*\. The replica secret remains linked to the primary secret\. See [Replicate an AWS Secrets Manager secret to other AWS Regions](create-manage-multi-region-secrets.md)\.

See [Create and manage secrets with AWS Secrets Manager](managing-secrets.md)\.

### Rotation<a name="term_rotation"></a>

*Rotation* is the process of periodically updating a secret to make it more difficult for an attacker to access the credentials\. In Secrets Manager, you can set up automatic rotation for your secrets\. When Secrets Manager rotates a secret, it updates the credentials in both the secret and the database or service\. See [Rotate AWS Secrets Manager secrets](rotating-secrets.md)\.

### Version<a name="term_version"></a>

A secret has *versions* which hold copies of the encrypted secret value\. When you change the secret value, or the secret is rotated, Secrets Manager creates a new version\. A secret always has a version with the staging label `AWSCURRENT`, which is the current secret value\.

During rotation, Secrets Manager uses staging labels to indicate the different versions of a secret:
+ `AWSCURRENT` indicates the version that is actively used by clients\. A secret always has an `AWSCURRENT` version\. 
+ `AWSPENDING` indicates the version that will become `AWSCURRENT` when rotation completes\.
+ `AWSPREVIOUS` indicates the *last known good* version, in other words, the previous `AWSCURRENT` version\.

Secrets Manager deprecates versions with no staging labels and removes them when there are more than 100\. Secrets Manager doesn't remove versions created less than 24 hours ago\.

When you use the AWS CLI or AWS SDK to get the secret value, you can specify the version of the secret\. If you don't specify a version, either by version ID or staging label, Secrets Manager gets the version with the staging label `AWSCURRENT` attached\.

You can also attach your own staging label to a version, for example to indicate development or production versions\. You can attach up to 20 staging labels to a secret\. Two versions of a secret can't have the same staging label\.