# Retrieving your secrets with the Parameter Store APIs<a name="integrating_parameterstore"></a>

AWS Systems Manager Parameter Store provides secure, hierarchical storage for configuration data management and secrets management\. You can store data such as passwords, database strings, and license codes as parameter values\. However, Parameter Store doesn't provide automatic rotation services for stored secrets\. Instead, Parameter Store enables you to store your secret in Secrets Manager, and then reference the secret as a Parameter Store parameter\.

When you configure Parameter Store with Secrets Manager, the `secret-id` Parameter Store requires a forward slash \(/\) before the name\-string\. 

For more information, see [Referencing AWS Secrets Manager Secrets from Parameter Store Parameters](https://docs.aws.amazon.com/systems-manager/latest/userguide/integration-ps-secretsmanager.html) in the *AWS Systems Manager User Guide*\.