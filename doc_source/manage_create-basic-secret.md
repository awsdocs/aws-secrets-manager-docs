# Creating a secret<a name="manage_create-basic-secret"></a>

A *secret* is a set of credentials, such as a user name and password, that you store in an encrypted form in Secrets Manager\. The secret also includes the connection information to access a database or other service, which Secrets Manager doesn't encrypt\.

You control access to the secret with IAM permission policies, which means that only authorized users can access or modify the secret\. Applications which access the database or other service use an IAM user or role, so you grant permission to that user or role to access the secret\. You can do this by resource or by identity:
+ You can attach a resource\-based policy to the secret and then in the policy, list the users or roles that have access\. For more information, see [Resource\-based policies](auth-and-access_resource-based-policies.md)\.
+ You can attach an identity\-based policy to a user or role, and then in the policy, list the secrets that the identity can access\. For more information, see [Identity\-based policies](auth-and-access_identity-based-policies.md)\.<a name="proc-create"></a><a name="rds-creds"></a><a name="redshift-creds"></a><a name="DocDB"></a><a name="nonrds-creds"></a><a name="other-creds"></a><a name="manage_create-basic-secret_console"></a><a name="manage_create-basic-secret_console.title"></a>

To create a secret, you need the permissions granted by the **SecretsManagerReadWrite** AWS managed policy\. For more information, see [Managed policies](reference_available-policies.md)\.

**To create a secret \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose **Store a new secret**\.

1. On the **Select secret type **page, do the following:

   1. For **Select secret type**, do one of the following:
      + To store database credentials, choose **Amazon RDS**, **Amazon DocumentDB**, **Amazon Redshift**, or **Other database**, and then enter the credentials you want to store\.
      + To store non\-database credentials or other information, choose **Other type of secrets**, and then in **Specify the key/value pairs to be stored in this secret**, do one of the following:
        + In **Key/value pairs**, enter the secret you want to store in **Key** and **Value** pairs\. You can add as many pairs as you need\. For example, you can specify **Key** **Username**, and then for **Value** enter the user name\. Add a second **Key** **Password**, and then for **Value** enter the password\. You can add pairs for **Database name**, **Server address**, **TCP port**, and so on\. 
        + On the **Plaintext** tab, enter your secret in any format\. 

   1. For **Encryption key**, choose the AWS KMS key that Secrets Manager uses to encrypt the protected text in the secret:
      + Choose **DefaultEncryptionKey** to use the AWS managed key for Secrets Manager\. There is no cost for using this key\. 
      + Choose another KMS key from the list\. You must have the following permissions: `kms:Encrypt`, `kms:Decrypt`, and `kms:GenerateDataKey`\.
      + Choose **Add new key** to go to the AWS KMS console to create a customer managed key\. You must have `kms:CreateKey` permission\. You will be charged for KMS keys that you create\. 

   1. If you chose database credentials in step 3a, for **Database**, enter your database connection information\.

   1. Choose **Next**\.

1. On the **Secret name and description** page, do the following:

   1. For **Secret name**, enter a name for your secret, for example **MyAppSecret** or **development/TestSecret**\. Use slashes to create a hierarchy for your secrets\. 

   1. \(Optional\) For **Description**, enter information to help you remember the purpose of this secret\.

   1. \(Optional\) In the **Tags** section, add tags to your secret\. For tagging strategies, see [Tag your secrets](best-practice_tagging.md)\. Don't store sensitive information in tags because they aren't encrypted\.

   1. \(Optional\) In **Resource permissions**, to add a resource policy to your secret, choose **Edit permissions**\. For more information, see [Resource\-based policies](auth-and-access_resource-based-policies.md)\.

   1. \(Optional\) In **Replicate secret**, to replicate your secret to another AWS Region, choose **Replicate secret to other Regions**\. For more information, see [Multi\-Region secrets](create-manage-multi-region-secrets.md)\.

   1. Choose **Next**\.

1. \(Optional\) On the **Configure automatic rotation** page, you can turn on automatic rotation\. You can also keep rotation off for now and then turn it on later\. For more information, see [Rotating secrets](rotating-secrets.md)\. Choose **Next**\.

1. On the **Review** page, review your secret details, and then choose **Store**\.

## AWS CLI<a name="proc-create-api"></a>

To create a secret by using the AWS CLI, you first create a JSON file or binary file that contains your secret\. Then you use the [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/create-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/create-secret.html) operation\.

If you want Secrets Manager to rotate the secret, your secret must be in the format described in [Rotation function templates](reference_available-rotation-templates.md)\. Otherwise, you can store your secret in any format\.

**To create a secret that uses the AWS managed key for Secrets Manager**

1. Create your secret in a file, for example a JSON file named **mycreds\.json**\.

   ```
   {
         "username": "saanvi",
         "password": "aDM4N3*!8TT"
   }
   ```

1. In the AWS CLI, use the following command\.

   ```
   $ aws secretsmanager create-secret --name production/MyAwesomeAppSecret --secret-string file://mycreds.json
   ```

   The following shows the output\.

   ```
   {
       "SecretARN": "arn:aws:secretsmanager:region:accountid:secret:production/MyAwesomeAppSecret-AbCdEf",
       "SecretName": "production/MyAwesomeAppSecret",
       "SecretVersionId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE"
   }
   ```

**To create a secret that uses a customer managed key**
+ In your AWS CLI command, include the [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CreateSecret.html#SecretsManager-CreateSecret-request-KmsKeyId](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CreateSecret.html#SecretsManager-CreateSecret-request-KmsKeyId) parameter, as shown in the following example\.

  ```
  aws secretsmanager create-secret --name production/MyAwesomeAppSecret --secret-string file://mycreds.json --kms-key-id MyKMSKey
  ```

## AWS SDK<a name="manage_create-basic-secret_SDK"></a>

To create a secret by using one of the AWS SDKs, use the [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CreateSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CreateSecret.html) action\. For more information, see:
+ [C\+\+](http://sdk.amazonaws.com/cpp/api/LATEST/namespace_aws_1_1_secrets_manager.html)
+ [Java](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/package-summary.html)
+ [PHP](https://docs.aws.amazon.com/aws-sdk-php/v3/api/namespace-Aws.SecretsManager.html)
+ [Python](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html)
+ [Ruby](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/SecretsManager.html)
+ [Node\.js](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/SecretsManager.html)