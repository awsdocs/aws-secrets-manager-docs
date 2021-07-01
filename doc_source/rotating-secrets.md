# Rotating your AWS Secrets Manager secrets<a name="rotating-secrets"></a>

You can configure AWS Secrets Manager to automatically rotate the secret for a secured service or database\. Secrets Manager natively knows how to rotate secrets for [supported Amazon RDS databases](intro.md#rds-supported-database-list)\. However, Secrets Manager also can enable you to rotate secrets for other databases or third\-party services\. Because each service or database can have a unique way of configuring secrets, Secrets Manager uses a Lambda function you can customize to work with a selected database or service\. You customize the Lambda function to implement the service\-specific details of rotating a secret\.

When you enable rotation for a secret by using the **Credentials for RDS database**, **Credentials for Amazon Redshift cluster**, or **Credentials for Amazon DocumentDB database** secret type, Secrets Manager provides a Lambda rotation function for you and populates the function automatically with the Amazon Resource Name \(ARN\) in the secret\. You provide a few details for this to work\. For example, you specify the secret with permissions to rotate the credentials, and how often you want to rotate the secret\.

When you enable rotation for a secret with **Credentials for other database** or some **Other type of secret**, you must provide the code for the Lambda function\. The code includes the commands required to interact with your secured service to update or add credentials\.

**Topics**
+ [Permissions required to automatically rotate secrets](rotating-secrets-required-permissions.md)
+ [Configuring your network to support rotating secrets](rotation-network-rqmts.md)
+ [Rotating secrets for supported Amazon RDS databases](rotating-secrets-rds.md)
+ [Rotating secrets for Amazon Redshift](rotating-secrets-redshift.md)
+ [Rotating secrets for Amazon DocumentDB](rotating-secrets-documentdb.md)
+ [Rotating AWS Secrets Manager secrets for other databases or services](rotating-secrets-other.md)
+ [Understanding and customizing your Lambda rotation function](rotating-secrets-lambda-function-customizing.md)
+ [AWS templates you can use to create Lambda rotation functions](reference_available-rotation-templates.md)
+ [Deleting unused Lambda rotation functions](rotating-secrets-managing-functions.md)