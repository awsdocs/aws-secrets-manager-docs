# Configure primary and replica secrets<a name="multi-region-config"></a>

Before you can configure multi\-Region secrets, you must enable the regions where you want to set up the replica secrets\. For more information, see [Managing AWS Regions\.](https://docs.aws.amazon.com/general/latest/gr/rande-manage.html#rande-manage-enable)

**Minimum permissions**  
To create a secret in the console, you must have these permissions:  
The permissions granted by the **SecretsManagerReadWrite** AWS managed policy\.
The permissions granted by the **IAMFullAccess** AWS managed policy – required only if you enable rotation for the secret\.
`kms:CreateKey` – required only if you want Secrets Manager to create a customer managed key\. 
`kms:Encrypt` – required only if you use a customer managed key to encrypt your secret instead of the AWS managed key \(`aws/secretsmanager`\) for your account\. You don't need this permission to use `aws/secretsmanager`\.
`kms:Decrypt` – required only if you use a customer managed key to encrypt your secret instead of the AWS managed key \(`aws/secretsmanager`\) for your account\. You don't need this permission to use `aws/secretsmanager`\.
`kms:GenerateDataKey` – required only if you use a custom AWS KMS key to encrypt your secret instead of the AWS managed key \(`aws/secretsmanager`\) for your account\. You don't need this permission to use `aws/secretsmanager`\.

**To replicate a secret to other Regions \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the **Secrets** page, choose your secret\.

1. On the Secret details page, do one of the following:
   + If your secret is not replicated, choose **Replicate secret to other Regions**\.
   + If your secret is replicated, in the **Replicate secret** section, choose **Add more regions**\.

1. In the **Add replica regions** dialog box, do the following:

   1. For **AWS Region**, choose the Region you want to replicate the secret to\.

   1. \(Optional\) For **Encryption key**, choose a KMS key to encrypt the secret with\. You can choose the same key as the primary secret or a different one\.

   1. \(Optional\)To add another Region, choose **Add more regions**\.

   1. Choose Complete adding regions\.

If replication fails, see [Retry secret replication](retry-replica.md)\.

**To replicate a secret \(AWS CLI or SDK\)**
+ Use the following commands to create a secret and configure a replica secret:
  + **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ReplicateSecretToRegions.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ReplicateSecretToRegions.html) 
  + 
    + [C\+\+](http://sdk.amazonaws.com/cpp/api/LATEST/namespace_aws_1_1_secrets_manager.html)
    + [Java](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/package-summary.html)
    + [PHP](https://docs.aws.amazon.com/aws-sdk-php/v3/api/namespace-Aws.SecretsManager.html)
    + [Python](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html)
    + [Ruby](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/SecretsManager.html)
    + [Node\.js](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/SecretsManager.html)
  + **AWS CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/replicate-secret-to-regions.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/replicate-secret-to-regions.html)

    The following example replicates a secret in US West \(Oregon\) to US East \(N\. Virginia\)\. 

    ```
    $ aws secretsmanager replicate-secret-to-regions --secret-id production/DBWest --add-replica-regions region us-east-1        
    ```

    If replication fails, see [Retry secret replication](retry-replica.md)\.