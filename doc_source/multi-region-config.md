# Configuring primary and replica secrets<a name="multi-region-config"></a>

Before you can configure multi\-Region secrets, you must enable the regions where you want to set up the replica secrets\. For more information, see [Managing AWS Regions\.](https://docs.aws.amazon.com/general/latest/gr/rande-manage.html#rande-manage-enable)

**Minimum permissions**  
To create a secret in the console, you must have these permissions:  
The permissions granted by the **SecretsManagerReadWrite** AWS managed policy\.
The permissions granted by the **IAMFullAccess** AWS managed policy – required only if you enable rotation for the secret\.
`kms:CreateKey` – required only if you want Secrets Manager to create a customer managed key\. 
`kms:Encrypt` – required only if you use a customer managed key to encrypt your secret instead of the AWS managed key \(`aws/secretsmanager`\) for your account\. You don't need this permission to use `aws/secretsmanager`\.
`kms:Decrypt` – required only if you use a customer managed key to encrypt your secret instead of the AWS managed key \(`aws/secretsmanager`\) for your account\. You don't need this permission to use `aws/secretsmanager`\.
`kms:GenerateDataKey` – required only if you use a custom AWS KMS key to encrypt your secret instead of the AWS managed key \(`aws/secretsmanager`\) for your account\. You don't need this permission to use `aws/secretsmanager`\.

------
#### [ Using the Secrets Manager console ]

1. Log in to the Secrets Manager at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\. 

1. Choose **Store a new secret**\.

1. Choose the secret type\.

1. Enter the appropriate credentials for the secret type\.

1. Choose **DefaultEncryptionKey** from the **Select the encryption key** list\.

1. Choose the database instance if you selected a database secret\.

1. Choose **Next**\.

1. Enter a name for the secret in **Secret name**\.

1. \(Optional\) Enter a description, up to 250 characters, in the **Description** field\.

1. \(Optional\) Enter tags as a **Key** and a **Value \- *optional***\.

1. Under **Replicate Secret \- *optional***, choose **Replicate secret to other regions**\.

1. From the **AWS Region** list, select the Region or Regions to replicate the secret\. You can add multiple Regions during this step\. 

1. From the **Encryption Key** list, choose the key used to encrypt the secret\. You can choose the AWS managed key \(`aws/secretsmanager`\) or a customer managed key you create in AWS KMS\.

1. To add more Regions, choose **Add more Regions** and choose additional Regions and encryption keys\.

   You must enable a Region before you can select it from the list\. 

1. Choose **Next**\.

1. Choose **Disable rotation**\.

   You can replicate a primary secret without configuring rotation for it\.

   If you want to rotate the secret, choose **Enable Rotation** and add the appropriate information for rotating the secret\.

1. Choose **Next**\.

1. Review the settings\. You have enabled **Secret Replication**, and can view the list of **Replica Region** and the **Encryption Key** assigned to each Replica Region\.

1. Choose **Create Secret**\.

After you create the secret, you can view your secrets on the **Secrets** page\. A banner at the top of the console indicates if you successfully created the secret\.

If replication fails, Secrets Manager displays a red banner with the failure reason\. Choose **View Details**\. See [Retrying Replication](retry-replica.md) for more information\.

------
#### [ Using the AWS CLI or AWS SDK operations ]

You use the following commands to create a secret and configure a replica secret:
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CreateSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CreateSecret.html) 
+ 
  + [C\+\+](http://sdk.amazonaws.com/cpp/api/LATEST/namespace_aws_1_1_secrets_manager.html)
  + [Java](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/package-summary.html)
  + [PHP](https://docs.aws.amazon.com/aws-sdk-php/v3/api/namespace-Aws.SecretsManager.html)
  + [Python](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html)
  + [Ruby](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/SecretsManager.html)
  + [Node\.js](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/SecretsManager.html)
+ **AWS CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/create-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/create-secret.html)

An example of an AWS CLI command to perform the equivalent of the console\-based secret replica configuration\. This command assumes you create the secret in US West \(Oregon\) and want to replicate it to US East \(N\. Virginia\)\. 

```
$ aws secretsmanager create-secret --name production/DBWest --add-replica-regions kmskeyid 1234abcd-12ab-34cd-56ef-1234567890ab region us-east-1
```

If the replication fails, see [Retrying Replication](retry-replica.md) for more information\.

------