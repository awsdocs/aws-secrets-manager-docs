# Create a secret<a name="manage_create-basic-secret"></a>

A *secret* is a set of credentials, such as a user name and password, that you store in an encrypted form in Secrets Manager\. The secret also includes the connection information to access a database or other service, which Secrets Manager doesn't encrypt\. You can also include other sensitive information, for example, passwords hints or question\-and\-answer pairs you can use to recover your password\. Don't store this type of information in the `Description` or any other non\-encrypted part of the secret\.

If you store text in the secret, it usually takes the form of JSON key\-value string pairs, as shown in the following example:

```
{
  "engine": "mysql",
  "username": "user1",
  "password": "i29wwX!%9wFV",
  "host": "my-database-endpoint.us-east-1.rds.amazonaws.com",
  "dbname": "myDatabase",
  "port": "3306"
}
```

You control access to the secret with IAM permission policies, which means that only authorized users can access or modify the secret\. Applications which access the database or other service use an IAM user or role, so you grant permission to that user or role to access the secret\. You can do this by resource or by identity:
+ You can attach a resource\-based policy to the secret and then in the policy, list the users or roles that have access\. For more information, see [Attach a permissions policy to a secret](auth-and-access_resource-policies.md)\.
+ You can attach an identity\-based policy to a user or role, and then in the policy, list the secrets that the identity can access\. For more information, see [Attach a permissions policy to an identity](auth-and-access_iam-policies.md)\.

To create a secret, you need the permissions granted by the **SecretsManagerReadWrite** AWS managed policy\. For more information, see [AWS managed policy](reference_available-policies.md)\.

**To create a secret \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose **Store a new secret**\.

1. On the **Store a new secret **page, do the following:

   1. For **Secret type**, do one of the following:
      + To store database credentials, choose **Credentials for Amazon RDS database**, **Credentials for Amazon DocumentDB database**, **Credentials for Amazon Redshift cluster**, or **Credentials for other database**, and then enter the credentials you want to store\.
      + To store non\-database credentials or other information, choose **Other type of secret**, and then in **Key/value pairs**, do one of the following:
        + In **Key/value**, enter the secret you want to store in pairs\. 

          You can add as many pairs as you need\. For example, in the first field you can specify **Username**, and then in the second field enter the user name\. Add a row, and then enter **Password** and the password\. You can add pairs for **Database name**, **Server address**, **TCP port**, and so on\. You can store up to 65536 bytes in the secret\. 

          If you copy and paste, we recommend you confirm the text on the **Plaintext** tab\. This helps ensure no extra whitespace characters have been copied such as tabs, which appear in plaintext as **\\t**\.
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

   1. \(Optional\) In the **Tags** section, add tags to your secret\. For tagging strategies, see [Tag secrets](managing-secrets_tagging.md)\. Don't store sensitive information in tags because they aren't encrypted\.

   1. \(Optional\) In **Resource permissions**, to add a resource policy to your secret, choose **Edit permissions**\. For more information, see [Attach a permissions policy to a secret](auth-and-access_resource-policies.md)\.

   1. \(Optional\) In **Replicate secret**, to replicate your secret to another AWS Region, choose **Replicate secret**\. You can replicate your secret now or come back and replicate it later\. For more information, see [Replicate a secret to other Regions](create-manage-multi-region-secrets.md)\.

   1. Choose **Next**\.

1. \(Optional\) On the **Secret rotation** page, you can turn on automatic rotation\. You can also keep rotation off for now and then turn it on later\. For more information, see [Rotate secrets](rotating-secrets.md)\. Choose **Next**\.

1. On the **Review** page, review your secret details, and then choose **Store**\.

## AWS CLI<a name="proc-create-api"></a>

To create a secret by using the AWS CLI, you first create a JSON file or binary file that contains your secret\. Then you use the [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/create-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/create-secret.html) operation\.

If you want Secrets Manager to rotate the secret, your secret must be in the format described in [Rotation function templates](reference_available-rotation-templates.md)\. Otherwise, you can store your secret in any format\.

**To create a secret**

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
       "SecretARN": "arn:aws:secretsmanager:Region:AccountId:secret:production/MyAwesomeAppSecret-AbCdEf",
       "SecretName": "production/MyAwesomeAppSecret",
       "SecretVersionId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE"
   }
   ```

## AWS SDK<a name="manage_create-basic-secret_SDK"></a>

To create a secret by using one of the AWS SDKs, use the [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CreateSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CreateSecret.html) action\. For more information, see [AWS SDKs](asm_access.md#asm-sdks)\.