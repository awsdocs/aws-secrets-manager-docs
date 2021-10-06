# Automatically rotate an Amazon RDS, Amazon DocumentDB, or Amazon Redshift secret<a name="rotate-secrets_turn-on-for-db"></a>

Secrets Manager provides complete rotation templates for Amazon RDS, Amazon DocumentDB, and Amazon Redshift secrets\. For other types of secrets, see [Automatically rotate another type of secret](rotate-secrets_turn-on-for-other.md)\.

Another way to automatically rotate a secret is to use AWS CloudFormation to create the secret, and include `AWS::SecretsManager::RotationSchedule`\. See [Automate secret creation in AWS CloudFormation](https://docs.aws.amazon.com/secretsmanager/latest/userguide/integrating_cloudformation.html)\.

There are two [Rotation strategies](rotating-secrets_strategies.md) available as rotation templates: single user and alternating users\. You can also [Customize a rotation function](rotate-secrets_customize.md)\. 

Before you begin, you need the following:
+ A user with credentials to the database or service\.
+ A rotation strategy\. See [Rotation strategies](rotating-secrets_strategies.md)\.
+ If you use the [Alternating users rotation strategy](rotating-secrets_strategies.md#rotating-secrets-two-users), you need a separate secret that contains credentials that can update the rotating secret's credentials\.

**To turn on rotation for an Amazon RDS, Amazon DocumentDB, or Amazon Redshift secret \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the **Secrets** page, choose your secret\.

1. On the **Secret details** page, in the **Rotation configuration** section, choose **Edit rotation**\.

1. In the **Edit rotation configuration** dialog box, do the following:

   1. Choose **Enable automatic rotation**\.

   1. For **Select rotation interval**, choose the number of days to keep the secret before rotating it\. 

      If you use [Alternating users rotation strategy](rotating-secrets_strategies.md#rotating-secrets-two-users), the credentials in the previous version of the secret are still valid and can be used to access the database or service\. To meet compliance requirements, you might need to rotate your secrets more often\. For example, if your credential lifetime maximum is 90 days, then we recommend you set your rotation interval to 44 days\. That way both users' credentials will be updated within 90 days\.

   1. Do one of the following:
      + To have Secrets Manager create a rotation function for you based on the [Rotation function templates](reference_available-rotation-templates.md) for your secret, choose **Create a new Lambda function** and enter a name for your new function\. Secrets Manager adds "SecretsManager" to the beginning of your function name\.
      + To use a rotation function that you or Secrets Manager already created, choose **Use an existing Lambda function**\. You can reuse a rotation function you used for another secret if the rotation strategy is the same\.

   1. For **Select which secret will be used to perform the rotation**, do one of the following:
      + For the [Single user rotation strategy](rotating-secrets_strategies.md#rotating-secrets-one-user-one-password), choose **Use this secret / Single user rotation**\.
      + For the [Alternating users rotation strategy](rotating-secrets_strategies.md#rotating-secrets-two-users), choose **Use a secret I previously stored / Multi\-user rotation**\. 

For help resolving common rotation issues, see [Troubleshoot AWS Secrets Manager rotation of secrets](troubleshoot_rotation.md)\.

## AWS SDK and AWS CLI<a name="rotating-secrets-built-in_cli"></a>

To turn on rotation, see:
+ **API/SDK:** [RotateSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RotateSecret.html)
+ **AWS CLI:** [rotate\-secret](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/rotate-secret.html)