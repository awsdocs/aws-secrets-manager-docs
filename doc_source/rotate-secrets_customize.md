# Customize a Lambda rotation function for Secrets Manager<a name="rotate-secrets_customize"></a>

For [Amazon RDS, Amazon DocumentDB, or Amazon Redshift secret](rotate-secrets_turn-on-for-db.md), Secrets Manager can create rotation functions for you for the [Single user](rotating-secrets_strategies.md#rotating-secrets-one-user-one-password) or [Alternating users](rotating-secrets_strategies.md#rotating-secrets-two-users) rotation strategies\. Secrets Manager uses [Secrets Manager rotation function templates](reference_available-rotation-templates.md) to create rotation functions\. 

You can modify those rotation functions, for example, if you need to test that a rotated secret works for more than read\-only access, or to create a different rotation strategy\. To change or delete the rotation function that rotates your secret, you first need the name of the function\. Then you can download it from the AWS Lambda console to edit it\. 

For information about what Secrets Manager expects in the rotation function, see [How rotation works](rotate-secrets_how.md) and [Using AWS Lambda with Secrets Manager](https://docs.aws.amazon.com/lambda/latest/dg/with-secrets-manager.html)\. 

**To find the rotation function for a secret \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. From the list of secrets, choose your secret\.

1. In the **Rotation configuration** section, in the rotation ARN, the part that follows `:function:` is the name of the function\.

**To find the rotation function for a secret \(AWS CLI\)**
+ 

  ```
  $ aws secretsmanager describe-secret --secret-id SecretARN
  ```

**To edit a Lambda function**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Choose your Lambda rotation function\.

1.  On the **Function code** menu, choose **Export function**\.

1. In the **Export your function** dialog box, choose **Download deployment package**\.

1. In your development environment, from the downloaded package, open `lambda_function.py`\. 