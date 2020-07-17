# AWS Services Integrated with AWS Secrets Manager<a name="integrating"></a>

AWS Secrets Manager works with other AWS services to provide additional solutions for your business challenges\. This topic identifies services that either use Secrets Manager to add functionality, or services that Secrets Manager uses to perform tasks\.

**Topics**
+ [Automating Creation of Your Secrets with AWS CloudFormation](integrating_cloudformation.md)
+ [Monitoring Secrets Manager Secrets Using AWS Config](integrating_awsconfig.md)
+ [Securing Your Secrets with AWS Identity and Access Management \(IAM\)](#integrating_iam)
+ [Monitoring Your Secrets with AWS CloudTrail and Amazon CloudWatch](#integrating_ct_cw)
+ [Encrypting Your Secrets with AWS KMS](#integrating_kms)
+ [Retrieving Your Secrets with the Parameter Store APIs](#integrating_parameterstore)
+ [Integrating Secrets Manager with Amazon Elastic Container Service](#integrating-ecs)
+ [Integrating Secrets Manager with AWS Fargate](#integrating-fargate)
+ [Integrating Secrets Manager with AWS IoT Greengrass](#integrating-greengrass)
+ [Managing Amazon SageMaker Repository Credentials with Secrets Manager ](#integrating-sagemaker)
+ [Storing AWS CodeBuild Registry Credentials with Secrets Manager](#integrating-codebuild)
+ [Storing Amazon EMR Registry Credentials with Secrets Manager](#integrating-emr)

## Automating Creation of Your Secrets with AWS CloudFormation<a name="integrating_cloudformation-overview"></a>

Secrets Manager supports AWS CloudFormation and enables you to define and reference secrets from within your stack template\. Secrets Manager defines several AWS CloudFormation resource types that allow you to create a secret and associate it with the service or database with credentials stored in it\. You can refer to elements in the secret from other parts of the template, such as retrieving the user name and password from the secret when you define the master user and password in a new database\. You can create and attach resource\-based policies to a secret\. You can also configure rotation by defining a Lambda function in your template and associating the function with your new secret as its rotation Lambda function\. For more information and a comprehensive example, see [Automating Secret Creation in AWS CloudFormation](integrating_cloudformation.md)\. 

## Monitoring Your Secrets with AWS Config<a name="integrating_awsconfig-overview"></a>

AWS Secrets Manager integrates with AWS Config and provides easier tracking of secret changes in Secrets Manager\. You can use two additional managed AWS Config [rules ](https://docs.aws.amazon.com/config/latest/developerguide/evaluate-config.html)for defining your organizational internal guidelines on secrets management best practices\. Also, you can quickly identify secrets that don't conform to your security rules as well as receive notifications from Amazon SNS about your secrets configuration changes\. For example, you can receive SNS notifications about a list of secrets unconfigured for rotation which enables you to drive security best practices for rotating secrets\.

When you leverage the AWS Config Aggregator feature, you can view a list of secrets and verify conformity across all accounts and regions in your entire organization, and by doing so, create secrets management best practices across your organization from a single location\.

To learn more about this feature see [Monitoring Secrets Manager Secrets Using AWS Config](integrating_awsconfig.md)\.

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

## Integrating Secrets Manager with Amazon Elastic Container Service<a name="integrating-ecs"></a>

Amazon ECS enables you to inject sensitive data into your containers by storing your sensitive data in Secrets Manager secrets and then referencing them in your container definition\. Sensitive data stored in Secrets Manager secrets can be exposed to a container as environment variables or as part of the log configuration\.

For a complete description of the integration, see [Specifying Sensitive Data Secrets\.](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/specifying-sensitive-data-secrets.html)

[Tutorial: Specifying Sensitive Data Using Secrets Manager Secrets](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/specifying-sensitive-data-tutorial.html)

## Integrating Secrets Manager with AWS Fargate<a name="integrating-fargate"></a>

AWS Fargate is a technology that you can use with Amazon ECS to run containers without managing servers or clusters of Amazon ECS instances\. With AWS Fargate, you no longer have to provision, configure, or scale clusters of virtual machines to run containers\. This removes the need to choose server types, decide when to scale your clusters, or optimize cluster packing\.

When you run your Amazon Amazon ECS tasks and services with the Fargate launch type or a Fargate capacity provider, you package your application in containers, specify the CPU and memory requirements, define networking and IAM policies, and launch the application\. Each Fargate task has its own isolation boundary and does not share the underlying kernel, CPU resources, memory resources, or elastic network interface with another task

You can configure Fargate interfaces to allow retrieval of secrets from Secrets Manager\. For more information, see [Fargate Task Networking](https://docs.aws.amazon.com/AmazonECS/latest/userguide/fargate-task-networking.html)

## Integrating Secrets Manager with AWS IoT Greengrass<a name="integrating-greengrass"></a>

AWS IoT Greengrass lets you authenticate with services and applications from Greengrass devices without hard\-coding passwords, tokens, or other secrets\.

You can use AWS Secrets Manager to securely store and manage your secrets in the cloud\. AWS IoT Greengrass extends Secrets Manager to Greengrass core devices, so your connectors and Lambda functions can use local secrets to interact with services and applications\. For example, the Twilio Notifications connector uses a locally stored authentication token\.

To integrate a secret into a Greengrass group, you create a group resource that references the Secrets Manager secret\. This secret resource references the cloud secret by using the associated ARN\. To learn how to create, manage, and use secret resources, see [Working with Secret Resources](https://docs.aws.amazon.com/greengrass/latest/developerguide/secrets-using.html) in the AWS IoT Developer Guide\. 

To deploy secrets to the AWS IoT Greengrass Core, see [Deploy secrets to the AWS IoT Greengrass core\.](https://docs.aws.amazon.com/greengrass/latest/developerguide/secrets.html)

## Managing Amazon SageMaker Repository Credentials with Secrets Manager<a name="integrating-sagemaker"></a>

Amazon SageMaker is a fully managed machine learning service\. With Amazon SageMaker, data scientists and developers can quickly and easily build and train machine learning models, and then directly deploy them into a production\-ready hosted environment\. It provides an integrated Jupyter authoring notebook instance for easy access to your data sources for exploration and analysis, so you don't have to manage servers\. It also provides common machine learning algorithms that are optimized to run efficiently against extremely large data in a distributed environment\. With native support for bring\-your\-own\-algorithms and frameworks, Amazon SageMaker offers flexible distributed training options that adjust to your specific workflows\. Deploy a model into a secure and scalable environment by launching it with a single click from the Amazon SageMaker console\.

You can manage your private repositories credentials using Secrets Manager\.You can manage your private repositories credentials using Secrets Manager\.

For more information, see [Associate Git Repositories with Amazon SageMaker Notebook Instances](https://docs.aws.amazon.com/sagemaker/latest/dg/nbi-git-repo.html)\.

## Storing AWS CodeBuild Registry Credentials with Secrets Manager<a name="integrating-codebuild"></a>

AWS CodeBuild is a fully managed build service in the cloud\. CodeBuild compiles your source code, runs unit tests, and produces artifacts ready to deploy\. CodeBuild eliminates the need to provision, manage, and scale your own build servers\. It provides prepackaged build environments for popular programming languages and build tools such as Apache Maven, Gradle, and more\. You can also customize build environments in CodeBuild to use your own build tools\. CodeBuild scales automatically to meet peak build requests\.

You can store your private registry credentials using Secrets Manager\. See [Private registry with AWS Secrets Manager sample for CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/sample-private-registry.html)\.

## Storing Amazon EMR Registry Credentials with Secrets Manager<a name="integrating-emr"></a>

Amazon EMR is a managed cluster platform that simplifies running big data frameworks, such as Apache Hadoop and Apache Spark, on AWS to process and analyze vast amounts of data\. By using these frameworks and related open\-source projects, such as Apache Hive and Apache Pig, you can process data for analytics purposes and business intelligence workloads\. Additionally, you can use Amazon EMR to transform and move large amounts of data into and out of other AWS data stores and databases, such as Amazon Simple Storage Service \(Amazon S3\) and Amazon DynamoDB\. 

You can store your private Git\-based registry credentials using Secrets Manager\. See [Add a Git\-based Repository to Amazon EMR](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-git-repo-add.html)\.