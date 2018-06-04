# What Is AWS Secrets Manager?<a name="intro"></a>

AWS Secrets Manager is an AWS service that makes it easier for you to manage secrets\. *Secrets* can be database credentials, passwords, third\-party API keys, and even arbitrary text\. You can store and control access to these secrets centrally by using the Secrets Manager console, the Secrets Manager command line interface \(CLI\), or the Secrets Manager API and SDKs\.

In the past, when you created a custom application that retrieves information from a database, you typically had to embed the credentials \(the secret\) for accessing the database directly in the application\. When it came time to rotate the credentials, you had to do much more than just create new credentials\. You had to invest time to update the application to use the new credentials\. Then you had to distribute the updated application\. If you had multiple applications that shared credentials and you missed updating one of them, the application would break\. Because of this risk, many customers have chosen not to regularly rotate their credentials, which effectively substitutes one risk for another\.

Secrets Manager enables you to replace hardcoded credentials in your code \(including passwords\), with an API call to Secrets Manager to retrieve the secret programmatically\. This helps ensure that the secret can't be compromised by someone examining your code, because the secret simply isn't there\. Also, you can configure Secrets Manager to automatically rotate the secret for you according to a schedule that you specify\. This enables you to replace long\-term secrets with short\-term ones, which helps to significantly reduce the risk of compromise\.

## Getting Started with Secrets Manager<a name="intro-getting-started"></a>

For a list of terms and concepts that you need to understand to make full use of Secrets Manager, see [Key Terms and Concepts for AWS Secrets Manager](terms-concepts.md)\.

Typical users of Secrets Manager fall into one or more of the following roles:
+ Secrets Manager administrator – Administers the Secrets Manager service\. Grants permissions to individuals who can then perform the other roles listed here\.
+ Database or service administrator – Administers the database or service whose secrets are stored in Secrets Manager\. Determines and configures the rotation and expiration settings for the secrets they own\. 
+ Application developer – Creates the application, and configures it to request the appropriate credentials from Secrets Manager\.

## Basic Secrets Manager Scenario<a name="intro-basic-scenario"></a>

The following diagram illustrates the most basic scenario\. It shows how you can store credentials for a database in Secrets Manager, and then use those credentials in an application that needs to access the database\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/ASM-Basic-Scenario.png)

1. The database administrator creates a set of credentials on the Personnel database for use by an app called MyCustomApp\. The administrator also configures those credentials with the permissions that are required for the app to access the Personnel database\.

1. The database administrator stores those credentials as a secret in Secrets Manager named *MyCustomAppCreds*\. The credentials are encrypted and stored within the secret as the *protected secret text*\.

1. When MyCustomApp needs to access the database, the app queries Secrets Manager for the secret named *MyCustomAppCreds*\.

1. Secrets Manager retrieves the secret, decrypts the protected secret text, and returns it to the client app over a secured \(HTTPS with TLS\) channel\.

1. The client app parses the credentials, connection string, and any other required information from the response and then uses that information to access the database server\.

**Note**  
Secrets Manager knows how to work with many types of secrets\. However, Secrets Manager can *natively* rotate credentials for [select AWS databases](#full-rotation-support) without requiring any additional programming\. However, rotating the secrets for other databases or services requires you to create a custom Lambda function to define how Secrets Manager interacts with the database or service\. You need some programming skill to create that function\. For more information, see [Rotating Your AWS Secrets Manager Secrets](rotating-secrets.md)\.

## Features of Secrets Manager<a name="features"></a>

### Programmatically Retrieve Encrypted Secret Values at Runtime Instead of Storing Them<a name="features_retrieve-by-label"></a>

One of the most important reasons to use Secrets Manager is to help you improve your security posture by removing hard\-coded credentials from your app's source code, and by not storing credentials in or with the app itself, in any way\. Storing the credentials in or with the app subjects them to possible compromise by anyone who can inspect your app or its components\. It also makes rotating your credentials difficult at best\. This is because you have to update your app and deploy the changes to every client before you can deprecate the old credentials\. 

Secrets Manager enables you to replace stored credentials with a runtime call to the Secrets Manager web service, so you can retrieve the credentials dynamically when you need them\. 

Most of the time, your client simply wants to access the most recent version of the encrypted secret value\. When you query for the encrypted secret value, you can choose to provide only the secret's name or Amazon Resource Name \(ARN\), without specifying any version information at all\. If you do this, Secrets Manager automatically returns the most recent version of the secret value\.

However, other versions can exist at the same time\. Most systems support secrets that are more complicated than a simple password—such as full sets of credentials that include the connection details, the user ID, and the password\. Secrets Manager allows you to have multiple sets of these credentials that exist at the same time\. Each set is stored in a different version of the secret\. During the secret rotation process, Secrets Manager tracks the older credentials that you're replacing, as well as the new credentials that you want to start using, until the rotation is complete\. It tracks these different versions by using *staging labels*\.

### Store Just About Any Kind of Secret<a name="features_storing-secrets"></a>

Secrets Manager enables you to store text up to 4096 bytes in length in the encrypted secret data portion of a secret\. This typically includes the connection details of the database or service\. These details can include the server name, IP address, and port number, as well as the user name and password that are used to sign in to the service\. The protected text doesn't include the secret name and description, the rotation or expiration settings, the ARN of the AWS KMS customer master key that's used to encrypt and decrypt the secret, or any AWS tags that you might attach\.

Secrets Manager encrypts the protected text of a secret by using [AWS Key Management Service \(AWS KMS\)](http://docs.aws.amazon.com/kms/latest/developerguide/), a key storage and encryption service that's used by many AWS services\. This helps ensure that your secret is securely encrypted when it's at rest\. In addition, Secrets Manager, by default, only accepts requests from hosts that use the open standard [Transport Layer Security \(TLS\)](https://en.wikipedia.org/wiki/Transport_Layer_Security) and [Perfect Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy)\. This helps ensure that your secret is also encrypted while it's in transit between AWS and the computers that you use to retrieve the secret\.

Secrets Manager can use a default AWS KMS customer master key \(CMK\) that you own and control for all of the secrets in your account\. Or you can specify individual CMKs for any secrets that require a different key\.

### Automatically Rotate Your Secrets<a name="features_autorotate"></a>

You can configure Secrets Manager to automatically rotate your secrets without any user intervention and on a schedule that you specify\.

You define and implement rotation with an AWS Lambda function\. This function defines how Secrets Manager creates a new version of the secret, stores it in Secrets Manager, configures the protected service to use the new version, verifies that the new version works, and then marks the new version as production ready\.

Staging labels help you to keep track of the different versions of your secrets\. Each version can have multiple staging labels attached, but a given staging label can be attached to only one version\. For example, the currently active and in\-use version of the secret is labeled `AWSCURRENT`\. Your apps typically should be configured to always query for that version of the secret\. When the rotation process creates a new version of a secret, it automatically adds the staging label `AWSPENDING` to the new version until it's tested and validated\. Only then does Secrets Manager move the `AWSCURRENT` staging label to this new version\. Your apps immediately start using the new secret the next time they query for the `AWSCURRENT` version\.

#### Databases with Fully Configured and Ready\-to\-Use Rotation Support<a name="full-rotation-support"></a>

When you choose to enable rotation, the following Amazon Relational Database Service \(Amazon RDS\) databases are fully supported with AWS written and tested Lambda rotation function templates, and full configuration of the rotation process:
+ Amazon Aurora
+ MySQL
+ PostgreSQL

You can also store secrets for almost any other kind of database or service\. However, to automatically rotate them you'll need to create and configure a custom Lambda rotation function yourself\. For more information about writing a custom Lambda function for a database or service, see [Overview of the Lambda Rotation Function](rotating-secrets-lambda-function-overview.md)\. 

### Control Who Can Access Secrets<a name="features_control-access"></a>

You can attach AWS Identity and Access Management \(IAM\) permission policies to your users, groups, and roles that grant or deny access to specific secrets, and restrict what they can do with those secrets\. For example, you might attach one policy to a group whose members need the ability to fully manage and configure your secrets\. Another policy attached to a role that's used by an application might grant only read permission on the one secret that the application needs to run\.

## Compliance with Standards<a name="asm_compliance"></a>

AWS Secrets Manager has undergone auditing for the following standards and can be part of your solution when you need to obtain compliance certification\.


|  |  | 
| --- |--- |
| ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/HIPAA.jpg) |  AWS has expanded its Health Insurance Portability and Accountability Act \(HIPAA\) compliance program to include AWS Secrets Manager as a [HIPAA Eligible Service](https://aws.amazon.com/compliance/hipaa-eligible-services-reference/)\. If you have an executed Business Associate Agreement \(BAA\) with AWS, you can use Secrets Manager to help build your HIPAA\-compliant applications\. AWS offers a [HIPAA\-focused Whitepaper](https://d0.awsstatic.com/whitepapers/compliance/AWS_HIPAA_Compliance_Whitepaper.pdf) for customers who are interested in learning more about how they can leverage AWS for the processing and storage of health information\. For more information, see [HIPAA Compliance](https://aws.amazon.com/compliance/hipaa-compliance/)  | 

## Accessing Secrets Manager<a name="asm_access"></a>

You can work with Secrets Manager in any of the following ways:

**AWS Management Console**  
[The Secrets Manager console](https://console.aws.amazon.com/secretsmanager/) is a browser\-based interface that you can use to manage your secrets\. You can perform almost any task that's related to your secrets by using the console\.  
Currently, you can't do the following in the console:  
+ *Store binary data in a secret\.* The console currently stores data only in the `SecureString` field of the secret, and doesn't use the `SecureBinary` field\. To store binary data, you must currently use the AWS CLI or one of the AWS SDKs\. 

**AWS Command Line Tools**  
The AWS command line tools let you issue commands at your system's command line to perform Secrets Manager and other AWS tasks\. This can be faster and more convenient than using the console\. The command line tools also are useful if you want to build scripts that perform AWS tasks\.  
AWS provides two sets of command line tools: the [AWS Command Line Interface](https://aws.amazon.com/cli/) \(AWS CLI\) and the [AWS Tools for Windows PowerShell](https://aws.amazon.com/powershell/)\. For information about installing and using the AWS CLI, see the [AWS Command Line Interface User Guide](http://docs.aws.amazon.com/cli/latest/userguide/)\. For information about installing and using the Tools for Windows PowerShell, see the [AWS Tools for Windows PowerShell User Guide](http://docs.aws.amazon.com/powershell/latest/userguide/)\.

**AWS SDKs**  
The AWS SDKs consist of libraries and sample code for various programming languages and platforms \(for example, [Java](https://aws.amazon.com/sdk-for-java/), [Python](https://aws.amazon.com/sdk-for-python/), [Ruby](https://aws.amazon.com/sdk-for-ruby/), [\.NET](https://aws.amazon.com/sdk-for-net/), [iOS and Android](https://aws.amazon.com/mobile/resources/), and [others](https://aws.amazon.com/tools/#sdk)\)\. The SDKs include tasks such as cryptographically signing requests, managing errors, and retrying requests automatically\. For more information about the AWS SDKs, including how to download and install them, see [Tools for Amazon Web Services](https://aws.amazon.com/tools/#sdk)\.

**Secrets Manager HTTPS Query API**  
The Secrets Manager HTTPS Query API gives you programmatic access to Secrets Manager and AWS\. The HTTPS Query API lets you issue HTTPS requests directly to the service\. When you use the HTTPS API, you must include code to digitally sign requests by using your credentials\. For more information, see [Calling the API by Making HTTP Query Requests](http://docs.aws.amazon.com/secretsmanager/latest/userguide/query-requests.html) and the [AWS Secrets Manager API Reference](http://docs.aws.amazon.com/secretsmanager/latest/apireference/)\.  
We recommend that you use the SDK that's specific to the programming language you prefer instead of using the HTTPS Query API\. The SDK performs many useful tasks that you otherwise must do manually\. One example is that the SDKs automatically sign your requests and convert the response into a structure that's syntactically appropriate to your language\. Use the HTTPS Query API only when an SDK isn't available\.

## Pricing for Secrets Manager<a name="asm_pricing"></a>

When you use Secrets Manager, you pay only for what you use, and there are no minimum or setup fees\. For the current complete pricing list, see [AWS Secrets Manager Pricing](https://aws.amazon.com/secrets-manager/pricing)\.

### AWS KMS – Custom Encryption Keys<a name="pricing-kms"></a>

If you create your own customer master keys by using AWS KMS to encrypt your secrets, then you're charged at the current AWS KMS rate\. However, you can use the "default" key that AWS Secrets Manager creates for your account for free\. For more information about the cost of customer\-created AWS KMS keys, see [AWS Key Management Service Pricing](https://aws.amazon.com/kms/pricing)\.

### AWS CloudTrail Logging – Storage and Notification<a name="pricing-cloudtrail"></a>

If you enable AWS CloudTrail on your account, you can obtain logs of API calls that AWS Secrets Manager makes\. There's no additional charge for AWS CloudTrail\. However, you can incur charges for Amazon S3 for log storage and for Amazon SNS if you enable notification\. For more information, see the [AWS CloudTrail](https://aws.amazon.com/cloudtrail/pricing) pricing page\.