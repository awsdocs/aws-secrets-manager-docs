# Key Terms and Concepts for AWS Secrets Manager<a name="terms-concepts"></a>

The following terms and concepts are important for understanding AWS Secrets Manager and how it works\.

## Secret<a name="term_secret"></a>

In Secrets Manager, a secret consists of a set of credentials, user name and password, and the connection details used to access a [secured service](#term_secured-service)\. You want to store these securely, and ensure only authorized users can access them\. Secrets Manager always stores the secret text in an encrypted form and encrypts the secret in transit\. 

Secrets Manager uses IAM permission policies to ensure only authorized users can access or modify the secret\. You can attach these policies to users or roles, and specify which secrets the users can access\. For more details about controlling access to your secrets, see [Authentication and Access Control for AWS Secrets Manager](auth-and-access.md)\.

When storing credentials, different secured services might require different pieces of information\. Secrets Manager provides this flexibility by storing the secret as key\-value pairs of text strings\. If you choose a database supported by Secrets Manager , Secrets Manager defines the key\-value pairs according to the requirements of the rotation function for the chosen database\. Secrets Manager formats the pairs as [JSON](http://json.org) text\. If you choose some other service or database Secrets Manager doesn't provide the Lambda function for, then you can specify your secret as a user\-defined JSON key\-value pairs\.

The resulting stored encrypted secret text might then resemble the following example:

```
{
  "host" : "ProdServer-01.databases.example.com",
  "port" : "8888",
  "username"   : "administrator",
  "password"   : "My-P@ssw0rd!F0r+Th3_Acc0unt",
  "dbname"     : "MyDatabase",
  "engine"     : "mysql"
}
```

If you use the command\-line tools or the API, you can also store binary data in the secret\. The Secrets Manager console does not support binary data \.

Secrets Manager can automatically rotate your secret for you on a specified schedule\. You can rotate credentials without interrupting the service if you choose to store a complete set of credentials for a user or account, instead of only the password\. If you change or rotate only the password, then the old password immediately becomes obsolete, and clients must immediately start using the new password or fail\. If you can instead create a new user with a new password, or at least alternate between two users, then the old user and password can continue to operate side by side with the new one, until you choose to deprecate the old one\. This gives you a window of time when all of your clients can continue to work while you test and validate the new credentials\. After your new credentials pass testing, you commit all of your clients to using the new credentials and remove the old credentials\.

**Supported Databases**  
If you use the Secrets Manager console and specify a secret for [one of the databases that Secrets Manager natively supports](intro.md#full-rotation-support), then Secrets Manager manages all of the structure and parsing for you\. The console prompts you for the details for the specific type of database \. Secrets Manager then constructs the necessary structure, stores the information, and then parses the information back into easy\-to\-understand text information when you retrieve it\. 

**Other Databases or Services**  
If you instead specify the secret for a "custom" database or service, then you control what you do with the secret text after you retrieve it and how you interpret it \. The Secrets Manager console accepts your secret as key\-value strings, and automatically converts them into a JSON structure for storage\. If you retrieve the secret in the console, Secrets Manager automatically parses the secret back into key\-value text strings for you to view\. If you retrieve the secret programmatically, then you can use an appropriate JSON parsing library, available for almost every programming language, to parse the secret in any way useful to you\. If a secret requires more than per\-secret limit of  65,536 bytes you could split your key\-value pairs between two secrets and concatenate them back together when you retrieve them\.

### Basic Structure of a Secrets Manager Secret<a name="basic-structure"></a>

In Secrets Manager, a secret contains not only the encrypted secret text, but also several metadata elements that describe the secret and define how Secrets Manager should handle the secret:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/secret-concept.png)
+ **Metadata – Details about the secret**
  + Basic information includes the name of the secret, a description, and the Amazon Resource Name \(ARN\) to serve as a unique identifier\. 
  + The ARN of the AWS Key Management Service \(AWS KMS\) key Secrets Manager uses to encrypt and decrypt the protected text in the secret\. If you don't provide this information, Secrets Manager uses the default AWS KMS key for the account\. 
  + Information about how frequently [to rotate](#term_rotation) the key and what Lambda function to use to perform the rotation\.
  + A user\-provided set of [tags](https://docs.aws.amazon.com/awsconsolehelpdocs/latest/gsg/tag-editor.html)\. You can attach tags as key\-value pairs to AWS resources for organizing, logical grouping, and [cost allocation](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html#allocation-what)\.
+ **Versions – A collection of one or more [versions](#term_version) of the encrypted secret text**
  + Although you typically only have one version of the secret active at a time, multiple versions can exist while you rotate a secret on the database or service\. Whenever you change the secret, Secrets Manager creates a new version\. 
  + Each version holds a copy of the encrypted secret value\.
  + Each version can have one or more [staging labels](#term_staging-label) attached identifying the stage of the secret rotation cycle\.

## Secured Service<a name="term_secured-service"></a>

Secrets Manager defines a secured service as a database or other service running on a network server, with access controlled by the credentials stored in the secret\. The secured service can refer to a single server or a large group of servers sharing the same access method\. You need the secret to successfully access the secured service\. The secret contains all of the information a client needs to access the secured service\. This guide uses the term "secured service" as a generic term to represent all of the different types of databases and services with secrets that can be protected by AWS Secrets Manager\. 

## Rotation<a name="term_rotation"></a>

Secrets Manager defines rotation as the process where you periodically change the secret to make it more difficult for an attacker to access the secured service\. With Secrets Manager, you don't have to manually change the secret and update it on all of your clients\. Instead, Secrets Manager uses an AWS Lambda function to perform all of the steps of rotation for you on a regular schedule\.

Suppose you have a large set of clients all running an application accessing a database, the [secured service](#term_secured-service)\. Instead of hardcoding the credentials into your application, the application sends a request to Secrets Manager and receive the secret details whenever needed\. When you rotate the secret, the Lambda rotation function automatically performs the following steps:

1. The rotation function contacts the secured service authentication system and creates a new set of credentials to access the database\. The credentials typically consist of a user name, a password, and connection details, but can vary from system to system\. Secrets Manager stores these new credentials as the secret text in a new [version](#term_version) of the secret with the `AWSPENDING` staging label attached\.

1. The rotation function then tests the `AWSPENDING` version of the secret to ensure that the credentials work, and grants the required level of access to the secured service\. 

1. If the tests succeed, the rotation function then moves the label `AWSCURRENT` to the new version to mark it as the default version\. Then, all of the clients start using this version of the secret instead of the old version\. The function also assigns the label `AWSPREVIOUS` to the old version, which marks it as the "last known good" version\. The version with the `AWSPREVIOUS` staging label now has no label, and therefore deprecated\.

You can trigger the Lambda rotation function manually when you choose **Rotate secret** in the console, or you can trigger it automatically every ***n*** days by specifying a rotation schedule\. If you use [one of the AWS databases that Secrets Manager natively supports](intro.md#full-rotation-support), then Secrets Manager provides a Lambda function to rotate the database credentials\. This function performs basic rotation for you automatically or you can customize the function to support an advanced custom rotation strategy\.

If you choose to create a secret for a custom service, then you must create the Lambda function \. In the code of the function, you determine how to compose the JSON structure and parse it in your function\.

For any service or database your secret uses, the Lambda rotation function for the secret must be able to access both your database or service and a Secrets Manager service endpoint\. If the Lambda rotation function and database or service reside in a VPC provided by Amazon VPC, then you must configure the VPC with either a [VPC service endpoint for Secrets Manager](rotation-network-rqmts.md), or [direct Internet connectivity by using a NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html), to allow access to the public Secrets Manager service endpoint\.

For more information about rotation, see [Rotating Your AWS Secrets Manager Secrets](rotating-secrets.md)\.

## Staging Labels<a name="term_staging-label"></a>

Secrets Manager uses staging labels, a simple text string, to enable you to identify different [versions](#term_version) of a secret during [rotation](#term_rotation)\. Whenever you query for the encrypted secret value, you can specify the version of the secret to retrieve\. If you don't specify a version either by version ID or staging label, Secrets Manager defaults to the version with the staging label `AWSCURRENT` attached\. Secrets Manager always attaches the staging label `AWSCURRENT` to one version of the secret\. See the brief introduction to [rotation](#term_rotation) for an example of how this works\.

A version of a secret can have from 0 to 20 staging labels attached\.

A staging label can be attached to only one version of a secret at a time\. Two versions of the secret can't have the same staging label\. When you attach a staging label to a version and a different version exists with the same label, you must also specify the version to remove the label, or Secrets Manager returns an error\.

One version of the secret must **always** have the staging label `AWSCURRENT`, and the API operation enforces this behavior\. The Lambda rotation functions provided by Secrets Manager automatically maintain the `AWSPENDING`, `AWSCURRENT`, and `AWSPREVIOUS` labels on the appropriate versions\.

## Versioning<a name="term_version"></a>

Multiple versions of a secret exist to support [rotation of a secret](#term_rotation)\. Secrets Manager distinguishes between different versions by the [staging labels](#term_staging-label)\. For most scenarios, you don't worry about versions of the secret\. Secrets Manager and the provided Lambda rotation function manage these details for you\. However, if you create a Lambda rotation function, your code must manage multiple versions of a secret and move the staging labels between versions appropriately\. Versions also have a unique identifier \(typically a [UUID](https://wikipedia.org/wiki/UUID) value\) that always stays with the same version, unlike staging labels you can move between versions\. 

Configure your clients to always request for the default version of the secret with the `AWSCURRENT` label attached\. Other versions can exist, but you only access other version by requesting a specific version ID or staging label\. If you request the secret value and you don't specify either a version ID or a staging label, then by default you see the version with the staging label `AWSCURRENT`\.

During rotation, Secrets Manager creates a new version of the secret and attaches the staging label `AWSPENDING`\. The rotation function uses the `AWSPENDING` version to identify the version until after the version passes testing\. After the rotation function verifies the new credentials work, Secrets Manager moves the label `AWSPREVIOUS` to the older version with `AWSCURRENT`, moves the label `AWSCURRENT` to the newer `AWSPENDING` version, and finally removes `AWSPENDING`\.

For more information about how staging labels work to support rotation, see [Rotating Your AWS Secrets Manager Secrets](rotating-secrets.md)\.

Each version maintained for a secret contains the following elements:
+ A unique ID for the version\.
+ A collection of staging labels used to identify the version, unique within the secret\. Secrets Manager deprecates versions with no staging labels and these versions may be subject to deletion\.
+ The encrypted and stored secret text\.

Whenever you query for the encrypted secret value, you can specify the version of the secret\. If you don't specify a version, either by version ID or staging label, Secrets Manager defaults to the version with the staging label `AWSCURRENT` attached\. Secrets Manager always attaches the staging label `AWSCURRENT` to one version of the secret\.