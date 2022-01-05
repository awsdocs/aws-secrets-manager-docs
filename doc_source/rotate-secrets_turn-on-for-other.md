# Automatically rotate another type of secret<a name="rotate-secrets_turn-on-for-other"></a>

Secrets Manager provides complete rotation templates for Amazon RDS, Amazon DocumentDB, and Amazon Redshift secrets\. For more information, see [Automatically rotate an Amazon RDS, Amazon DocumentDB, or Amazon Redshift secret](rotate-secrets_turn-on-for-db.md)\.

For other types of secrets, you create your own rotation function\. Secrets Manager provides a [Generic rotation function template](reference_available-rotation-templates.md#sar-template-generic) that you can use as a starting point\. If you use the Secrets Manager console or AWS Serverless Application Repository console to create your function from the template, then the Lambda execution role is also automatically set up\. 

Another way to automatically rotate a secret is to use AWS CloudFormation to create the secret, and include `AWS::SecretsManager::RotationSchedule`\. See [Automate secret creation in AWS CloudFormation](https://docs.aws.amazon.com/secretsmanager/latest/userguide/integrating_cloudformation.html)\.

 

Before you begin, you need the following:
+ A secret with the information you want to rotate, for example credentials for a user of a database or service\.

**To turn on rotation \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the **Secrets** page, choose your secret\.

1. On the **Secret details** page, in the **Rotation configuration** section, choose **Edit rotation**\. The **Edit rotation configuration** dialog box opens\. Do the following:

   1. Choose **Enable automatic rotation**\.

   1. For **Select rotation interval**, choose the number of days to keep the secret before rotating it\.

   1. For **Choose a Lambda function**, do one of the following:

      1. If you already created a rotation function for this type of secret, choose it\.

      1. Otherwise, choose **Create function**\. In the Lambda console, create your new rotation function\. If you see **Browse serverless app repository**, choose it, choose **Show apps that create custom IAM roles or resource policies**, and then choose **SecretsManagerRotationTemplate**\. Otherwise, choose **Author from scratch** and use the [Generic rotation function template](reference_available-rotation-templates.md#sar-template-generic) as a starting point for your function\. Implement each of the steps described in [How rotation works](rotate-secrets_how.md)\.

         When your function is complete, return to the Secrets Manager console to finish your secret\. For **Choose a Lambda function**, choose the refresh button\. Then in the list of functions, choose your new function\.

   1. Choose **Save**\.

For help resolving common rotation issues, see [Troubleshoot AWS Secrets Manager rotation of secrets](troubleshoot_rotation.md)\.

## AWS SDK and AWS CLI<a name="rotating-secrets-other_cli"></a>

To turn on rotation, see [rotate\-secret](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/rotate-secret.html)\.

## AWS SDK<a name="rotating-secrets-other_sdk"></a>

To turn on rotation, use the [RotateSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RotateSecret.html) action\. For more information, see [AWS SDKs](asm_access.md#asm-sdks)\.