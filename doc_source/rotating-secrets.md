# Rotating Your AWS Secrets Manager Secrets<a name="rotating-secrets"></a>

You can configure AWS Secrets Manager to automatically rotate the secret for a secured service or database\. AWS Secrets Manager already natively knows how to rotate secrets for [supported Amazon RDS databases](rotating-secrets-rds.md#rds-supported-database-list)\. However, AWS Secrets Manager also can enable you to rotate secrets for other databases or third\-party services\. Because each service or database can have a unique way of configuring its secrets, Secrets Manager uses a Lambda function that you can customize to work with whatever database or service that you choose\. You customize the Lambda function to implement the service\-specific details of how to rotate a secret\.

When you enable rotation for a secret using the **Credentials for RDS database** secret type, Secrets Manager provides a Lambda rotation function for you and populates the function's ARN in the secret automatically\. You typically don't need to do anything for this to work other than to provide a few details, such as identifying which secret has permissions to rotate the credentials, and how often you want to rotate the secret\.

When you enable rotation for a secret with **Credentials for other database** or some **Other type of secret**, you must provide the code for the Lambda function\. The code includes the commands required to interact with your secured service to update or add credentials\.

**Topics**
+ [Rotating Secrets for Supported Amazon RDS Databases](rotating-secrets-rds.md)
+ [Rotating AWS Secrets Manager Secrets for Other Databases or Services](rotating-secrets-other.md)
+ [Understanding and Customizing Your Lambda Rotation Function](rotating-secrets-lambda-function-customizing.md)