# Rotating Your AWS Secrets Manager Secrets<a name="rotating-secrets"></a>

You can configure AWS Secrets Manager to automatically rotate the secret for a secured service or database\. Secrets Manager natively knows how to rotate secrets for [supported Amazon RDS databases](intro.md#rds-supported-database-list)\. However, Secrets Manager also can enable you to rotate secrets for other databases or third\-party services\. Because each service or database can have a unique way of configuring secrets, Secrets Manager uses a Lambda function you can customize to work with a selected database or service\. You customize the Lambda function to implement the service\-specific details of rotating a secret\.

When you enable rotation for a secret by using the **Credentials for RDS database**, **Credentials for Redshift cluster**, or **Credentials for DocumentDB database** secret type, Secrets Manager provides a Lambda rotation function for you and populates the function automatically with the Amazon Resource Name \(ARN\) in the secret\. You provide a few details for this to work\. For example, you specify the secret with permissions to rotate the credentials, and how often you want to rotate the secret\.

When you enable rotation for a secret with **Credentials for other database** or some **Other type of secret**, you must provide the code for the Lambda function\. The code includes the commands required to interact with your secured service to update or add credentials\.

**Topics**
+ [Permissions Required to Automatically Rotate Secrets](rotating-secrets-required-permissions.md)
+ [Configuring Your Network to Support Rotating Secrets](rotation-network-rqmts.md)
+ [Rotating Secrets for Supported Amazon RDS Databases](rotating-secrets-rds.md)
+ [Rotating Secrets for Amazon Redshift](rotating-secrets-redshift.md)
+ [Rotating Secrets for Amazon DocumentDB](rotating-secrets-documentdb.md)
+ [Rotating AWS Secrets Manager Secrets for Other Databases or Services](rotating-secrets-other.md)
+ [Understanding and Customizing Your Lambda Rotation Function](rotating-secrets-lambda-function-customizing.md)
+ [Deleting Unused Lambda Rotation Functions](rotating-secrets-managing-functions.md)