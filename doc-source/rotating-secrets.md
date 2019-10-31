# Rotating Your AWS Secrets Manager Secrets<a name="rotating-secrets"></a>

You can configure AWS Secrets Manager to automatically rotate the secret for a secured service or database\. Secrets Manager already natively knows how to rotate secrets for [supported Amazon RDS databases](intro.md#rds-supported-database-list)\. However, Secrets Manager also can enable you to rotate secrets for other databases or third\-party services\. Because each service or database can have a unique way of configuring its secrets, Secrets Manager uses a Lambda function that you can customize to work with whatever database or service that you choose\. You customize the Lambda function to implement the service\-specific details of how to rotate a secret\.

When you enable rotation for a secret by using the **Credentials for RDS database**, **Credentials for Redshift cluster**, or **Credentials for DocumentDB database** secret type, Secrets Manager provides a Lambda rotation function for you and populates the function's Amazon Resource Name \(ARN\) in the secret automatically\. You typically don't need to do anything for this to work other than to provide a few details\. For example, you specify which secret has permissions to rotate the credentials, and how often you want to rotate the secret\.

When you enable rotation for a secret with **Credentials for other database** or some **Other type of secret**, you must provide the code for the Lambda function\. The code includes the commands that are required to interact with your secured service to update or add credentials\.

**Topics**
+ [Permissions Required to Automatically Rotate Secrets](rotating-secrets-required-permissions.md)
+ [Configuring Your Network to Support Rotating Secrets](rotation-network-rqmts.md)
+ [Rotating Secrets for Supported Amazon RDS Databases](rotating-secrets-rds.md)
+ [Rotating Secrets for Amazon Redshift](rotating-secrets-redshift.md)
+ [Rotating Secrets for Amazon DocumentDB](rotating-secrets-documentdb.md)
+ [Rotating AWS Secrets Manager Secrets for Other Databases or Services](rotating-secrets-other.md)
+ [Understanding and Customizing Your Lambda Rotation Function](rotating-secrets-lambda-function-customizing.md)
+ [Deleting Lambda Rotation Functions That You No Longer Need](rotating-secrets-managing-functions.md)