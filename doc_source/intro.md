# What Is AWS Secrets Manager?<a name="intro"></a>

In the past, when you created a custom application to retrieve information from a database, you typically embedded the credentials, the secret, for accessing the database directly in the application\. When the time came to rotate the credentials, you had to do more than just create new credentials\. You had to invest time to update the application to use the new credentials\. Then you distributed the updated application\. If you had multiple applications with shared credentials and you missed updating one of them, the application failed\. Because of this risk, many customers choose not to regularly rotate credentials, which effectively substitutes one risk for another\.

Secrets Manager enables you to replace hardcoded credentials in your code, including passwords, with an API call to Secrets Manager to retrieve the secret programmatically\. This helps ensure the secret can't be compromised by someone examining your code, because the secret no longer exists in the code\. Also, you can configure Secrets Manager to automatically rotate the secret for you according to a specified schedule\. This enables you to replace long\-term secrets with short\-term ones, significantly reducing the risk of compromise\.

## Getting Started with Secrets Manager<a name="intro-getting-started"></a>

For a list of terms and concepts you need to understand to make full use of Secrets Manager, see [Key Terms and Concepts for AWS Secrets Manager](terms-concepts.md)\.

Typical users of Secrets Manager can have one or more of the following roles:
+ Secrets Manager administrator – Administers the Secrets Manager service\. Grants permissions to individuals who can then perform the other roles listed here\.
+ Database or service administrator – Administers the database or service with secrets stored in Secrets Manager\. Determines and configures the rotation and expiration settings for their secrets\. 
+ Application developer – Creates the application, and then configures the application to request the appropriate credentials from Secrets Manager\.

## Basic Secrets Manager Scenario<a name="intro-basic-scenario"></a>

The following diagram illustrates the most basic scenario\. The diagram displays you can store credentials for a database in Secrets Manager, and then use those credentials in an application to access the database\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/ASM-Basic-Scenario.png)

1. The database administrator creates a set of credentials on the Personnel database for use by an application called MyCustomApp\. The administrator also configures those credentials with the permissions required for the application to access the Personnel database\.

1. The database administrator stores the credentials as a secret in Secrets Manager named *MyCustomAppCreds*\. Then, Secrets Manager encrypts and stores the credentials within the secret as the *protected secret text*\.

1. When MyCustomApp accesses the database, the application queries Secrets Manager for the secret named *MyCustomAppCreds*\.

1. Secrets Manager retrieves the secret, decrypts the protected secret text, and returns the secret to the client app over a secured \(HTTPS with TLS\) channel\.

1. The client application parses the credentials, connection string, and any other required information from the response and then uses the information to access the database server\.

**Note**  
Secrets Manager supports many types of secrets\. However, Secrets Manager can *natively* rotate credentials for [supported AWS databases](#full-rotation-support) without any additional programming\. However, rotating the secrets for other databases or services requires creating a custom Lambda function to define how Secrets Manager interacts with the database or service\. You need some programming skill to create the function\. For more information, see [Rotating Your AWS Secrets Manager Secrets](rotating-secrets.md)\.

## Features of Secrets Manager<a name="features"></a>

### Programmatically Retrieve Encrypted Secret Values at Runtime<a name="features_retrieve-by-label"></a>

Secrets Manager helps you improve your security posture by removing hard\-coded credentials from your application source code, and by not storing credentials within the application, in any way\. Storing the credentials in or with the application subjects them to possible compromise by anyone who can inspect your application or the components\. Since you have to update your application and deploy the changes to every client before you can deprecate the old credentials, this process makes rotating your credentials difficult\. 

Secrets Manager enables you to replace stored credentials with a runtime call to the Secrets Manager Web service, so you can retrieve the credentials dynamically when you need them\. 

Most of the time, your client requires access to the most recent version of the encrypted secret value\. When you query for the encrypted secret value, you can choose to provide only the secret name or Amazon Resource Name \(ARN\), without specifying any version information at all\. If you do this, Secrets Manager automatically returns the most recent version of the secret value\.

However, other versions can exist at the same time\. Most systems support secrets more complicated than a simple password, such as full sets of credentials including the connection details, the user ID, and the password\. Secrets Manager allows you to store multiple sets of these credentials at the same time\. Secrets Manager stores each set in a different version of the secret\. During the secret rotation process, Secrets Manager tracks the older credentials, as well as the new credentials you want to start using, until the rotation completes\. It tracks these different versions by using *[staging labels](terms-concepts.md#term_staging-label)*\.

### Storing Different Types of Secrets<a name="features_storing-secrets"></a>

Secrets Manager enables you to store text in the encrypted secret data portion of a secret\. This typically includes the connection details of the database or service\. These details can include the server name, IP address, and port number, as well as the user name and password used to sign in to the service\. For details on secrets, see the [maximum and minimum values](reference_limits.html#reference_limits_max-min)\. The protected text doesn't include:
+  Secret name and description
+  Rotation or expiration settings
+  ARN of the AWS KMS customer master key \(CMK\) associated with the secret
+ Any attached AWS tags 

### Encrypting Your Secret Data<a name="features_kms-encryption"></a>

Secrets Manager encrypts the protected text of a secret by using [AWS Key Management Service \(AWS KMS\)](https://docs.aws.amazon.com/kms/latest/developerguide/)\. Many AWS services use AWS KMS for key storage and encryption\. AWS KMS ensures secure encryption of your secret when at rest\. Secrets Manager associates every secret with an AWS KMS CMK\. It can be either the default CMK for Secrets Manager for the account, or a customer\-created CMK\. 

Whenever Secrets Manager encrypt a new version of the protected secret data, Secrets Manager requests AWS KMS to generate a new data key from the specified CMK\. Secrets Manager uses this data key for [envelope encryption](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping)\. Secrets Manager stores the encrypted data key with the protected secret data\. Whenever the secret needs decryption, Secrets Manager requests AWS KMS to decrypt the data key, which Secrets Manager then uses to decrypt the protected secret data\. Secrets Manager never stores the data key in unencrypted form, and always disposes the data key immediately after use\.

In addition, Secrets Manager, by default, only accepts requests from hosts using open standard [Transport Layer Security \(TLS\)](https://en.wikipedia.org/wiki/Transport_Layer_Security) and [Perfect Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy)\. Secrets Manager ensures encryption of your secret while in transit between AWS and the computers you use to retrieve the secret\.

### Automatically Rotating Your Secrets<a name="features_autorotate"></a>

You can configure Secrets Manager to automatically rotate your secrets without user intervention and on a specified schedule\.

You define and implement rotation with an AWS Lambda function\. This function defines how Secrets Manager performs the following tasks:
+ Creates a new version of the secret\.
+ Stores the secret in Secrets Manager\.
+ Configures the protected service to use the new version\.
+ Verifies the new version\.
+ Marks the new version as production ready\.

Staging labels help you to keep track of the different versions of your secrets\. Each version can have multiple staging labels attached, but each staging label can only be attached to one version\. For example, Secrets Manager labels the currently active and in\-use version of the secret with `AWSCURRENT`\. You should configure your applications to always query for the current version of the secret\. When the rotation process creates a new version of a secret, Secrets Manager automatically adds the staging label `AWSPENDING` to the new version until testing and validation completes\. Only then does Secrets Manager add the `AWSCURRENT` staging label to this new version\. Your applications immediately start using the new secret the next time they query for the `AWSCURRENT` version\.

#### Databases with Fully Configured and Ready\-to\-Use Rotation Support<a name="full-rotation-support"></a>

When you choose to enable rotation, Secrets Manager supports the following Amazon Relational Database Service \(Amazon RDS\) databases with AWS written and tested Lambda rotation function templates, and full configuration of the rotation process:<a name="rds-supported-database-list"></a>
+ Amazon Aurora on Amazon RDS
+ MySQL on Amazon RDS
+ PostgreSQL on Amazon RDS
+ Oracle on Amazon RDS
+ MariaDB on Amazon RDS
+ Microsoft SQL Server on Amazon RDS

#### Other Services with Fully Configured and Ready\-to\-Use Rotation Support<a name="other-with-full-rotation-support"></a>

You can also choose to enable rotation on the following services, fully supported with AWS written and tested Lambda rotation function templates, and full configuration of the rotation process:<a name="other-supported-list"></a>
+ Amazon DocumentDB 
+ Amazon Redshift 

You can also store secrets for almost any other kind of database or service\. However, to automatically rotate the secrets, you need to create and configure a custom Lambda rotation function\. For more information about writing a custom Lambda function for a database or service, see [Overview of the Lambda Rotation Function](rotating-secrets-lambda-function-overview.md)\. 

### Control Access to Secrets<a name="features_control-access"></a>

You can attach AWS Identity and Access Management \(IAM\) permission policies to your users, groups, and roles that grant or deny access to specific secrets, and restrict management of those secrets\. For example, you might attach one policy to a group with members that require the ability to fully manage and configure your secrets\. Another policy attached to a role used by an application might grant only read permission on the one secret the application needs to run\.

Alternatively, you can attach a resource\-based policy directly to the secret to grant permissions specifying users who can read or modify the secret and the versions\. Unlike an identity\-based policy which automatically applies to the user, group, or role, a resource\-based policy attached to a secret uses the `Principal` element to identify the target of the policy\. The `Principal` element can include users and roles from the same account as the secret or principals from other accounts\.

## Accessing Secrets Manager<a name="asm_access"></a>

You can work with Secrets Manager in any of the following ways:

**AWS Management Console**  
You can manage your secrets using the browser\-based [The Secrets Manager console](https://console.aws.amazon.com/secretsmanager/) and perform almost any task related to your secrets by using the console\.  
Currently, you can't perform the following task in the console:  
+ *Store binary data in a secret\.* The console currently stores data only in the `SecretString` field of the secret, and does not use the `SecureBinary` field\. To store binary data, you must currently use the AWS CLI or one of the AWS SDKs\. 

**AWS Command Line Tools**  
The AWS command line tools allows you to issue commands at your system command line to perform Secrets Manager and other AWS tasks\. This can be faster and more convenient than using the console\. The command line tools can be useful if you want to build scripts to perform AWS tasks\.  
AWS provides two sets of command line tools: the [AWS Command Line Interface](https://aws.amazon.com/cli/) \(AWS CLI\) and the [AWS Tools for Windows PowerShell](https://aws.amazon.com/powershell/)\. For information about installing and using the AWS CLI, see the [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)\. For information about installing and using the Tools for Windows PowerShell, see the [AWS Tools for Windows PowerShell User Guide](https://docs.aws.amazon.com/powershell/latest/userguide/)\.

**AWS SDKs**  
The AWS SDKs consist of libraries and sample code for various programming languages and platforms, for example, [Java](https://aws.amazon.com/sdk-for-java/), [Python](https://aws.amazon.com/sdk-for-python/), [Ruby](https://aws.amazon.com/sdk-for-ruby/), [\.NET](https://aws.amazon.com/sdk-for-net/), [iOS and Android](https://aws.amazon.com/mobile/resources/), and [others](https://aws.amazon.com/tools/#sdk)\. The SDKs include tasks such as cryptographically signing requests, managing errors, and retrying requests automatically\. For more information about the AWS SDKs, including how to download and install them, see [Tools for Amazon Web Services](https://aws.amazon.com/tools/#sdk)\.

**Secrets Manager HTTPS Query API**  
The Secrets Manager HTTPS Query API gives you programmatic access to Secrets Manager and AWS\. The HTTPS Query API allows you to issue HTTPS requests directly to the service\. When you use the HTTPS API, you must include code to digitally sign requests by using your credentials\. For more information, see [Calling the API by Making HTTP Query Requests](https://docs.aws.amazon.com/secretsmanager/latest/userguide/query-requests.html) and the [AWS Secrets Manager API Reference](https://docs.aws.amazon.com/secretsmanager/latest/apireference/)\.  
We recommend using the SDK specific to the programming language you prefer instead of using the HTTPS Query API\. The SDK performs many useful tasks you perform manually\. The SDKs automatically sign your requests and convert the response into a structure syntactically appropriate to your language\. Use the HTTPS Query API only when an SDK is unavailable\.

## Pricing for Secrets Manager<a name="asm_pricing"></a>

When you use Secrets Manager, you pay only for what you use, and no minimum or setup fees\. For the current complete pricing list, see [AWS Secrets Manager Pricing](https://aws.amazon.com/secrets-manager/pricing)\.

### AWS KMS – Custom Encryption Keys<a name="pricing-kms"></a>

If you create your own customer master keys by using AWS KMS to encrypt your secrets, AWS charges you at the current AWS KMS rate\. However, you can use the "default" key created by AWS Secrets Manager for your account for free\. For more information about the cost of customer\-created AWS KMS keys, see [AWS Key Management Service Pricing](https://aws.amazon.com/kms/pricing)\.

### AWS CloudTrail Logging – Storage and Notification<a name="pricing-cloudtrail"></a>

If you enable AWS CloudTrail on your account, you can obtain logs of API calls AWS Secrets Manager sends out\. Secrets Manager logs all events as management events\. There are no data events\. There's no additional charge for capturing a single trail in AWS CloudTrail to capture management events\. AWS CloudTrail stores the first copy of all management events for free\. However, you can incur charges for Amazon S3 for log storage and for Amazon SNS if you enable notification\. Also, if you set up additional trails, the additional copies of management events can incur costs\. For more information, see the [AWS CloudTrail](https://aws.amazon.com/cloudtrail/pricing) pricing page\.