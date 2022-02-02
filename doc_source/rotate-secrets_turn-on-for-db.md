# Automatically rotate an Amazon RDS, Amazon DocumentDB, or Amazon Redshift secret<a name="rotate-secrets_turn-on-for-db"></a>

Secrets Manager provides complete rotation templates for Amazon RDS, Amazon DocumentDB, and Amazon Redshift secrets\. For other types of secrets, see [Automatically rotate another type of secret](rotate-secrets_turn-on-for-other.md)\. 

Rotation functions for Amazon RDS \(except Oracle\) and Amazon DocumentDB automatically use Secure Socket Layer \(SSL\) or Transport Layer Security \(TLS\) to connect to your database, if it is available\. Otherwise they use an unencrypted connection\.

**Note**  
If you set up automatic secret rotation before December 20, 2021, your rotation function might be based on an older template that did not support SSL/TLS\. See [Determine when your rotation function was created](troubleshoot_rotation.md#rotation-function-created-date)\. If it was created before December 20, 2021, to support connections that use SSL/TLS, you need to [recreate your rotation function](#rotate-secrets_turn-on-for-db)\.  
Edit your secret, and then choose **Edit rotation**\. In the dialog box, choose **Create a rotation function** to recreate your rotation function\. If you made [customizations](rotate-secrets_customize.md) to your previous rotation function, you must redo them in the new rotation function\.

Another way to automatically rotate a secret is to use AWS CloudFormation to create the secret, and include `AWS::SecretsManager::RotationSchedule`\. See [Automate secret creation in AWS CloudFormation](https://docs.aws.amazon.com/secretsmanager/latest/userguide/integrating_cloudformation.html)\.

Before you begin, you need the following:
+ A user with credentials to Amazon RDS, Amazon DocumentDB, or Amazon Redshift\.
+ A rotation strategy\. See [Rotation strategies](rotating-secrets_strategies.md)\.
+ If you use the [Alternating users rotation strategy](rotating-secrets_strategies.md#rotating-secrets-two-users), you need a separate secret that contains credentials that can update the rotating secret's credentials\.

**To turn on rotation for an Amazon RDS, Amazon DocumentDB, or Amazon Redshift secret \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the **Secrets** page, choose your secret\.

1. On the **Secret details** page, in the **Rotation configuration** section, choose **Edit rotation**\.

1. In the **Edit rotation configuration** dialog box, do the following:

   1. Turn on **Automatic rotation**\.

   1. Under **Rotation schedule**, enter your schedule in UTC time zone by doing one of the following:
      + Choose **Schedule expression builder** to build a schedule in a form\. Secrets Manager stores your schedule as a **rate\(\)** or **cron\(\)** expression\. The rotation window automatically starts at midnight unless you specify a **Start time**\. 
      + Choose **Schedule expression**, and then do one of the following:
        + Enter the *cron expression* for your schedule, for example, **cron\(0 21 L \* ? \*\)**, which rotates the secret on the last day of every month at 9:00 PM UTC\+0\. A cron expression for Secrets Manager must have **0** in the minutes field because Secrets Manager rotation windows open on the hour\. It must have **\*** in the year field, because Secrets Manager does not support rotation schedules that are more than a year apart\. For more information, see [Schedule expressions](rotate-secrets_schedule.md)\. 
        + Enter a *rate expression* for a daily rate, for example, **rate\(10 days\)**, which rotates the secret every 10 days\. The expression must include **rate\(\)**\. With a rate expression, the rotation window automatically starts at midnight\.

   1. \(Optional\) For **Window duration**, choose the length of the window during which you want Secrets Manager to rotate your secret, for example **3h** for a three hour window\. The window must not go into the next UTC day\. The rotation window automatically ends at the end of the day if you don't specify **Window duration**\. 

   1. \(Optional\) Choose **Rotate immediately when the secret is stored** to rotate your secret when you save your changes\. If you clear the checkbox, then the first rotation will begin on the schedule you set\.

      If you use [Alternating users rotation strategy](rotating-secrets_strategies.md#rotating-secrets-two-users), the credentials in the previous version of the secret are still valid and can be used to access the database or service\. To meet compliance requirements, you might need to rotate your secrets more often\. For example, if your credential lifetime maximum is 90 days, then we recommend you set your rotation interval to 44 days\. That way both users' credentials will be updated within 90 days\.

   1. Under **Rotation function**, do the following:
      + To have Secrets Manager create a rotation function for you based on the [Rotation function templates](reference_available-rotation-templates.md) for your secret, choose **Create a new Lambda function** and enter a name for your new function\. Secrets Manager adds "SecretsManager" to the beginning of your function name\.
      + To use a rotation function that you or Secrets Manager already created, choose **Use an existing Lambda function**\. You can reuse a rotation function you used for another secret if the rotation strategy is the same\.

   1. For **Use separate credentials to rotate this secret**, do one of the following:
      + For the [Single user rotation strategy](rotating-secrets_strategies.md#rotating-secrets-one-user-one-password), choose **No**\.
      + For the [Alternating users rotation strategy](rotating-secrets_strategies.md#rotating-secrets-two-users), choose **Yes**\. Then choose a secret that contains a user with elevated credentials\.

For help resolving common rotation issues, see [Troubleshoot AWS Secrets Manager rotation of secrets](troubleshoot_rotation.md)\.

## AWS CLI<a name="rotating-secrets-built-in_cli"></a>

To turn on rotation, see [rotate\-secret](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/rotate-secret.html)\.

## AWS SDK<a name="rotating-secrets-built-in_sdk"></a>

To turn on rotation, use the [RotateSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RotateSecret.html) action\. For more information, see [AWS SDKs](asm_access.md#asm-sdks)\.