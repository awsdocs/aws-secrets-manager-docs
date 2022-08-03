# Automatically rotate a secret<a name="rotate-secrets_turn-on-for-other"></a>

Secrets Manager provides complete rotation templates for Amazon RDS, Amazon DocumentDB, and Amazon Redshift secrets\. For more information, see [Automatically rotate an Amazon RDS, Amazon DocumentDB, or Amazon Redshift secret](rotate-secrets_turn-on-for-db.md)\.

For other types of secrets, you create your own rotation function\. Secrets Manager provides a [Other types of secrets](reference_available-rotation-templates.md#OTHER_rotation_templates) that you can use as a starting point\. If you use the Secrets Manager console or AWS Serverless Application Repository console to create your function from the template, then the Lambda execution role is also automatically set up\. 

Another way to automatically rotate a secret is to use AWS CloudFormation to create the secret, and include `AWS::SecretsManager::RotationSchedule`\. See [Automate secret creation in AWS CloudFormation](https://docs.aws.amazon.com/secretsmanager/latest/userguide/integrating_cloudformation.html)\.

 

Before you begin, you need the following:
+ A secret with the information you want to rotate, for example credentials for a user of a database or service\.

**To turn on rotation \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the **Secrets** page, choose your secret\.

1. On the **Secret details** page, in the **Rotation configuration** section, choose **Edit rotation**\. The **Edit rotation configuration** dialog box opens\. Do the following:

   1. Turn on **Automatic rotation**\.

   1. Under **Rotation schedule**, enter your schedule in UTC time zone by doing one of the following:
      + Choose **Schedule expression builder** to build a schedule in a form\. Secrets Manager stores your schedule as a **rate\(\)** or **cron\(\)** expression\. The rotation window automatically starts at midnight unless you specify a **Start time**\. 
      + Choose **Schedule expression**, and then do one of the following:
        + Enter the *cron expression* for your schedule, for example, **cron\(0 21 L \* ? \*\)**, which rotates the secret on the last day of every month at 9:00 PM UTC\+0\. A cron expression for Secrets Manager must have **0** in the minutes field because Secrets Manager rotation windows open on the hour\. It must have **\*** in the year field, because Secrets Manager does not support rotation schedules that are more than a year apart\. For more information, see [Schedule expressions](rotate-secrets_schedule.md)\. 
        + Enter a *rate expression* for a daily rate, for example, **rate\(10 days\)**, which rotates the secret every 10 days\. The expression must include **rate\(\)**\. With a rate expression, the rotation window automatically starts at midnight\.

   1. \(Optional\) For **Window duration**, choose the length of the window during which you want Secrets Manager to rotate your secret, for example **3h** for a three hour window\. The window must not go into the next UTC day\. The rotation window automatically ends at the end of the day if you don't specify **Window duration**\. 

   1. Under **Rotation function**, do one of the following:

      1. If you already created a rotation function for this type of secret, choose it\.

      1. Otherwise, choose **Create function**\. In the Lambda console, create your new rotation function\. If you see **Browse serverless app repository**, choose it, choose **Show apps that create custom IAM roles or resource policies**, and then choose **SecretsManagerRotationTemplate**\. Otherwise, choose **Author from scratch** and use the [Other types of secrets](reference_available-rotation-templates.md#OTHER_rotation_templates) as a starting point for your function\. Implement each of the steps described in [How rotation works](rotate-secrets_how.md)\.

         When your function is complete, return to the Secrets Manager console to finish your secret\. For **Choose a Lambda function**, choose the refresh button\. Then in the list of functions, choose your new function\.

   1. Choose **Save**\.

For help resolving common rotation issues, see [Troubleshoot AWS Secrets Manager rotation of secrets](troubleshoot_rotation.md)\.

## AWS SDK and AWS CLI<a name="rotating-secrets-other_cli"></a>

To turn on rotation, see [rotate\-secret](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/rotate-secret.html)\.

## AWS SDK<a name="rotating-secrets-other_sdk"></a>

To turn on rotation, use the [RotateSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RotateSecret.html) action\. For more information, see [AWS SDKs](asm_access.md#asm-sdks)\.