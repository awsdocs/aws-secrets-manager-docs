# Rotating AWS Secrets Manager Secrets for Other Databases or Services<a name="rotating-secrets-other"></a>

You can configure AWS Secrets Manager to automatically rotate the secret for a secured service or database\. Secrets Manager natively knows how to [rotate secrets for Amazon RDS databases](rotating-secrets-rds.md)\. However, Secrets Manager also enables you to rotate secrets for other databases or third\-party services\. Because each service or database can have a unique way of configuring its secrets, Secrets Manager uses a Lambda function that you must write to work with whatever database or service that you choose\. You customize the Lambda function to implement the service\-specific details of how to rotate a secret\.

When you enable rotation for a secret for another database or some other type of service, you must create and configure the Lambda function and write the code\.

Before you can enable rotation for other databases or services, you must first create the Lambda rotation function\. AWS does provide a generic template Lambda rotation function that you can use as a starting point\. The following procedure describes how to create a new Lambda function based on this template\. When you then [enable rotation](enable-rotation-other.md), you only need to add the Amazon Resource Name \(ARN\) of the completed function to your secret\.

**Topics**
+ [Rotating AWS Secrets Manager Secrets for Other Databases or Services](rotating-secrets-create-generic-template.md)
+ [Enabling Rotation for a Secret for Another Database or Service](enable-rotation-other.md)