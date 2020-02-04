# AWS Services Integrated with AWS Secrets Manager<a name="integrating"></a>

AWS Secrets Manager works with other AWS services to provide additional solutions for your business challenges\. This topic identifies services that either use Secrets Manager to add functionality, or services that Secrets Manager uses to perform tasks\.

**Topics**
+ [Automating Creation of Your Secrets with AWS CloudFormation](#integrating_cloudformation-overview)
+ [Securing Your Secrets with AWS Identity and Access Management \(IAM\)](#integrating_iam)
+ [Monitoring Your Secrets with AWS CloudTrail and Amazon CloudWatch](#integrating_ct_cw)
+ [Encrypting Your Secrets with AWS KMS](#integrating_kms)
+ [Retrieving Your Secrets with the Parameter Store APIs](#integrating_parameterstore)

## Automating Creation of Your Secrets with AWS CloudFormation<a name="integrating_cloudformation-overview"></a>

Secrets Manager supports AWS CloudFormation and enables you to define and reference secrets from within your stack template\. Secrets Manager defines several AWS CloudFormation resource types that allow you to create a secret and associate it with the service or database with credentials stored in it\. You can refer to elements in the secret from other parts of the template, such as retrieving the user name and password from the secret when you define the master user and password in a new database\. You can create and attach resource\-based policies to a secret\. You can also configure rotation by defining a Lambda function in your template and associating the function with your new secret as its rotation Lambda function\. For more information and a comprehensive example, see [Automating Secret Creation in AWS CloudFormation](integrating_cloudformation.md) 

## Securing Your Secrets with AWS Identity and Access Management \(IAM\)<a name="integrating_iam"></a>

Secrets Manager uses IAM to secure access to the secrets\. IAM provides the following:
+ **Authentication** – Verifies the identity of individuals requests\. Secrets Manager uses a sign\-in process with passwords, access keys, and multi\-factor authentication \(MFA\) tokens to prove the identify of the users\.
+ **Authorization** – Ensures only approved individuals can perform operations on AWS resources, such as secrets\. This enables you to grant write access to specific secrets to one user while granting read\-only permissions on different secrets to another user\.

For more information about protecting access to your secrets by using IAM, see [Authentication and Access Control for AWS Secrets Manager](auth-and-access.md) in this guide\. For complete information about IAM, see the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.

## Monitoring Your Secrets with AWS CloudTrail and Amazon CloudWatch<a name="integrating_ct_cw"></a>

You can use CloudTrail and CloudWatch to montor activity related to your secrets\. CloudTrail captures API activity for your AWS resources by any AWS service and writes the activity to log files in your Amazon S3 buckets\. CloudWatch enables you to create rules to monitor those log files and trigger actions when activities of interest occur\. For example, a text message can alert you whenever someone creates a new secret, or when a secret rotates successfully\. You could also create an alert for when a client attempts to use a deprecated version of a secret instead of the current version\. This can help with troubleshooting\.

For more information, see [Monitoring the Use of Your AWS Secrets Manager Secrets](monitoring.md) in this guide\. For complete information about CloudWatch, see the [Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)\. For complete information about CloudWatch Events, see the [Amazon CloudWatch Events User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/)\.

## Encrypting Your Secrets with AWS KMS<a name="integrating_kms"></a>

Secrets Manager uses the trusted, industry\-standard [Advanced Encryption Standard \(AES\) encryption algorithm \(FIPS 197\)](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.197.pdf) to encrypt your secrets\. Secrets Manager also uses encryption keys provided by AWS Key Management Service \(AWS KMS\) to perform [envelope encryption](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping) of the secret value\. When you create a new version of a secret, Secrets Manager uses the specified AWS KMS customer master key \(CMK\) to generate a new [data key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#data-keys)\. The data key consists of a 256\-bit symmetric AES key\. Secrets Manager receives both a plaintext and encrypted copy of the data key\. Secrets Manager uses the plaintext data key to encrypt the secret value, and then stores the encrypted data key in the metadata of the secret version\. When you later send a request to Secrets Manager to retrieve the secret value, Secrets Manager first retrieves the encrypted data key from the metadata, and then requests AWS KMS to decrypt the data key using the associated CMK\. AWS KMS uses the decrypted data key to decrypt the secret value, and never stores keys in an unencrypted state, and removes the keys from memory immediately when no longer required\.

For more information, see [How AWS Secrets Manager Uses AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/services-secrets-manager.html) in the *AWS Key Management Service Developer Guide*\.

## Retrieving Your Secrets with the Parameter Store APIs<a name="integrating_parameterstore"></a>

AWS Systems Manager Parameter Store provides secure, hierarchical storage for configuration data management and secrets management\. You can store data such as passwords, database strings, and license codes as parameter values\. However, Parameter Store doesn't provide automatic rotation services for stored secrets\. Instead, Parameter Store enables you to store your secret in Secrets Manager, and then reference the secret as a Parameter Store parameter\.

When you configure Parameter Store with Secrets Manager, the `secret-id` Parameter Store requires a forward slash \(/\) before the name\-string\. 

For more information, see [Referencing AWS Secrets Manager Secrets from Parameter Store Parameters](https://docs.aws.amazon.com/systems-manager/latest/userguide/integration-ps-secretsmanager.html) in the *AWS Systems Manager User Guide*\.