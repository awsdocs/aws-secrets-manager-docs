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
+ The ARN for an *encryption key*, an AWS KMS key that Secrets Manager uses to encrypt and decrypt the secret value\. Secrets Manager stores secret text in an encrypted form and encrypts the secret in transit\. See [Secret encryption and decryption in AWS Secrets Manager](security-encryption.md)\.
+ Information about how to rotate the secret, if you set up rotation\. See [Rotation](#term_rotation)\.

Secrets Manager uses IAM permission policies to make sure that only authorized users can access or modify a secret\. See [Authentication and access control for AWS Secrets Manager](auth-and-access.md)\.

A secret has *versions* which hold copies of the encrypted secret value\. When you change the secret value, or the secret is rotated, Secrets Manager creates a new version\. See [Version](#term_version)\.

You can use a secret across multiple AWS Regions by *replicating* it\. When you replicate a secret, you create a copy of the original or *primary secret* called a *replica secret*\. The replica secret remains linked to the primary secret\. See [Replicate an AWS Secrets Manager secret to other AWS Regions](create-manage-multi-region-secrets.md)\.

See [Create and manage secrets with AWS Secrets Manager](managing-secrets.md)\.

### Rotation<a name="term_rotation"></a>

*Rotation* is the process of periodically updating a secret to make it more difficult for an attacker to access the credentials\. In Secrets Manager, you can set up automatic rotation for your secrets\. When Secrets Manager rotates a secret, it updates the credentials in both the secret and the database or service\. See [Rotate AWS Secrets Manager secrets](rotating-secrets.md)\.

### Rotation strategy<a name="rotation-stragegy"></a>

**Topics**
+ [Single user](#rotating-secrets-one-user-one-password)
+ [Alternating users](#rotating-secrets-two-users)

#### Rotation strategy: single user<a name="rotating-secrets-one-user-one-password"></a>

This strategy updates credentials for one user in one secret\. The user must have permission to update their password\. This is the simplest rotation strategy, and it is appropriate for most use cases\. 

When the secret rotates, open database connections are not dropped\. While rotation is happening, there is a short period of time between when the password in the database changes and when the secret is updated\. During this time, there is a low risk of the database denying calls that use the rotated credentials\. You can mitigate this risk with an [appropriate retry strategy](http://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/)\. After rotation, new connections use the new credentials\. 

#### Rotation strategy: alternating users<a name="rotating-secrets-two-users"></a>

This strategy updates credentials for two users in one secret\. You create the first user, and during the first rotation, the rotation function clones it to create the second user\. Every time the secret rotates, the rotation function alternates which user's password it updates\. Because most users don't have permission to clone themselves, you must provide the credentials for a `superuser` in another secret\. If cloned users in your database don't have the same permissions as the original user, often called an *ad hoc* or *interactive* user, we recommend using the single\-user rotation strategy\.

This strategy is appropriate for databases with permission models where one role owns the database tables and a second role has permission to access the database tables\. It is also appropriate for applications that require high availability\. If an application retrieves the secret during rotation, the application still gets a valid set of credentials\. After rotation, both `user` and `user_clone` credentials are valid\. There is even less chance of applications getting a deny during this type of rotation than single user rotation\. If the database is hosted on a server farm where the password change takes time to propagate to all servers, there is a risk of the database denying calls that use the new credentials\. You can mitigate this risk with an [appropriate retry strategy](http://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/)\. 

### Version<a name="term_version"></a>

A secret has *versions* which hold copies of the encrypted secret value\. When you change the secret value, or the secret is rotated, Secrets Manager creates a new version\.

Secrets Manager doesn't store a history of secrets with versions\. Instead, it keeps track of the previous secret value by tagging that secret version with `AWSPREVIOUS`\. During rotation, Secrets Manager also tracks the next secret value by tagging that secret version with `AWSPENDING`\. A secret always has an `AWSCURRENT` version\. The `AWSCURRENT` version is what Secrets Manager returns when you retrieve the secret value, unless you specify a different version\. 

You can add other versions with your own labels by using [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecretVersionStage.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecretVersionStage.html) in the AWS CLI or an AWS SDK\. You can attach up to 20 staging labels to a secret\. Two versions of a secret can't have the same staging label\. If a version doesn't have a label, Secrets Manager considers it deprecated\. Secrets Manager removes deprecated secret versions when there are more than 100\. Secrets Manager doesn't remove versions created less than 24 hours ago\.