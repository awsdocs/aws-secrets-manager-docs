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

      1. Otherwise, choose **Create function**\. The Lambda console opens to the **Create function** page\.

      1. Choose **Browse serverless app repository**, in the search box enter **SecretsManagerRotationTemplate**, choose **Show apps that create custom IAM roles**, and then choose the **SecretsManagerRotationTemplate** card\.

      1. Edit **Application settings** as follows:

         1. For **Application name**, enter the appliation name for AWS Serverless Application Repository\.

         1. For **functionName**, enter the name of the Lambda function\. This is the name you see in the Secrets Manager console\.

         1. For **invokingServicePrincipal**, keep **secretsmanager\.amazonaws\.com**\.

         1. For **endpoint**, enter the endpoint for your secret's Region\. See [AWS Secrets Manager endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/asm.html)\.

         1. \(Optional\) For **excludeCharacters**, enter characters that you don't want to use in rotated passwords\.

         1. \(Optional\) For **kmsKeyArn**, enter the KMS key that encrypts the secret\. If you don't enter a key ARN, Secrets Manager uses the AWS managed key `aws/secretsmanager`\.

         1. \(Optional\) If your rotation strategy uses an elevated user to change credentials for another user, for **masterSecretArn**, enter the ARN of the secret that contains the elevated credentials\. See [Alternating users rotation strategy](rotating-secrets_strategies.md#rotating-secrets-two-users)\.

         1. \(Optional\) If your rotation strategy uses an elevated user to change credentials for another user, for **masterSecretKmsKeyArn**, enter the KMS key that encrypts the secret\. If you don't enter a key ARN, Secrets Manager uses the AWS managed key `aws/secretsmanager`\.

         1. \(Optional\) For **vpcSecurityGroupIds**, enter the VPC security group that your database or service uses\. See [Network access for the rotation function](rotation-network-rqmts.md)\.

         1. \(Optional\) For **vpcSubnetIds**, enter the VPC subnets that your database or service uses\. See [Network access for the rotation function](rotation-network-rqmts.md)\.

         1. Choose **I acknowledge that this app creates custom IAM roles and resource policies**, because this app creates a Lambda execution role that Secrets Manager uses to run the Lambda function\.

         1. Choose **Deploy**\.

         1. After the function deploys, under **Resources**, choose **SecretsManagerRotationTemplate** to open the function for editing\. Implement each of the steps described in [How rotation works](rotate-secrets_how.md)\. Use Python 3\.7\.

            When your function is complete, return to the Secrets Manager console to finish your secret\.

      1. For **Choose a Lambda function**, choose the refresh button\. Then in the list of functions, choose your new function\.

      1. Choose **Save**\.

For help resolving common rotation issues, see [Troubleshoot AWS Secrets Manager rotation of secrets](troubleshoot_rotation.md)\.

## AWS SDK and AWS CLI<a name="rotating-secrets-other_cli"></a>

To turn on rotation, see:
+ **API/SDK:** [RotateSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RotateSecret.html)
+ **AWS CLI:** [rotate\-secret](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/rotate-secret.html)