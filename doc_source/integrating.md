# AWS Services That Work with AWS Secrets Manager<a name="integrating"></a>

AWS Secrets Manager works with other AWS services to provide additional solutions for your business challenges\. This topic identifies services that either use Secrets Manager to add functionality, or services that Secrets Manager uses to perform its tasks\.

**Topics**
+ [Securing Your Secrets with AWS Identity and Access Management \(IAM\)](#integrating_iam)
+ [Monitoring Your Secrets with AWS CloudTrail and Amazon CloudWatch](#integrating_ct_cw)
+ [Encrypting Your Secrets with AWS KMS](#integrating_kms)
+ [Retrieving Your Secrets with the Parameter Store APIs](#integrating_parameterstore)

## Securing Your Secrets with AWS Identity and Access Management \(IAM\)<a name="integrating_iam"></a>

Secrets Manager uses IAM to secure access to its secrets\. IAM provides the following:
+ **Authentication** – Verifies the identity of individuals who make requests\. This is the sign\-in process that uses passwords, access keys, and multi\-factor authentication \(MFA\) tokens to prove that users are who they claim to be\.
+ **Authorization** – Ensures that only approved individuals can perform operations on AWS resources, such as secrets\. This enables you to grant write access to specific secrets to one user while granting read\-only permissions on different secrets to another user\.

For more information about how to protect access to your secrets by using IAM, see [Authentication and Access Control for AWS Secrets Manager](auth-and-access.md) in this guide\. For complete information about IAM, see the [IAM User Guide](http://docs.aws.amazon.com/IAM/latest/UserGuide/)\.

## Monitoring Your Secrets with AWS CloudTrail and Amazon CloudWatch<a name="integrating_ct_cw"></a>

You can use CloudTrail and CloudWatch to montor activity that's related to your secrets\. CloudTrail captures API activity for your AWS resources by any AWS service and writes it to log files in your Amazon S3 buckets\. CloudWatch enables you to create rules that monitor those log files and trigger actions when activities of interest occur\. For example, you can have a text message alert you whenever someone creates a new secret, or when a secret rotates successfully\. You could also create an alert for when a client attempts to use a deprecated version of a secret instead of the current version\. This can help with troubleshooting\.

For more information, see [Monitor the Use of Your AWS Secrets Manager Secrets](monitoring.md) in this guide\. For complete information about CloudWatch, see the [Amazon CloudWatch User Guide](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)\. For complete information about CloudWatch Events, see the [Amazon CloudWatch Events User Guide](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/)\.

## Encrypting Your Secrets with AWS KMS<a name="integrating_kms"></a>

Secrets Manager uses the trusted, industry\-standard [Advanced Encryption Standard \(AES\) encryption algorithm \(FIPS 197\)](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.197.pdf) to encrypt your secrets\. It uses encryption keys provided by AWS Key Management Service \(AWS KMS\) to perform [envelope encryption](http://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping) of the secret value\. When you create a new version of a secret, Secrets Manager uses the specified AWS KMS customer master key \(CMK\) to generate a new [data key](http://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#data-keys)\. The data key is a 256\-bit symmetric AES key\. Secrets Manager receives both a plaintext and encrypted copy of the data key\. The plaintext data key is used to encrypt the secret value\. Then the encrypted data key is stored in the metadata of the secret version\. When you later ask Secrets Manager to retrieve the secret value, Secrets Manager first retrieves the encrypted data key from the metadata, and then asks AWS KMS to decrypt the data key using the associated CMK\. The decrypted data key is then used to decrypt the secret value\. The keys are never stored in an unencrypted state, and are removed from memory immediately when they're no longer required\.

For more information, see [How AWS Secrets Manager Uses AWS KMS](http://docs.aws.amazon.com/kms/latest/developerguide/services-secrets-manager.html) in the *AWS Key Management Service Developer Guide*\.

## Retrieving Your Secrets with the Parameter Store APIs<a name="integrating_parameterstore"></a>

AWS Systems Manager Parameter Store provides secure, hierarchical storage for configuration data management and secrets management\. You can store data such as passwords, database strings, and license codes as parameter values\. However, Parameter Store doesn't provide automatic rotation services for the secrets it stores\. Instead, Parameter Store enables you to store your secret in Secrets Manager, and then reference that secret as a Parameter Store parameter\.

For more information, see [Referencing AWS Secrets Manager Secrets from Parameter Store Parameters](http://docs.aws.amazon.com/systems-manager/latest/userguide/integration-ps-secretsmanager.html) in the *AWS Systems Manager User Guide*\.