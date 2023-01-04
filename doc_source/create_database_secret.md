# Create an AWS Secrets Manager database secret<a name="create_database_secret"></a>

After you create a user in Amazon RDS, Amazon Aurora, Amazon Redshift, or Amazon DocumentDB, you can store their credentials in Secrets Manager by following these steps\. When you use the AWS CLI or one of the SDKs to store the secret, you must provide the secret in the [correct JSON structure](reference_secret_json_structure.md)\. When you use the console to store a database secret, Secrets Manager automatically creates it in the correct JSON structure\.

**Tip**  
For some [Secrets managed by other services](service-linked-secrets.md), you use *managed rotation*\. To use [Managed rotation](rotate-secrets_managed.md), you first create the secret through the managing service\.

When you store database credentials for a source database that is replicated to other Regions, the secret contains connection information for the source database\. If you then replicate the secret, the replicas are copies of the source secret and contain the same connection information\. You can add additional key/value pairs to the secret for regional connection information\.

To create a secret, you need the permissions granted by the **SecretsManagerReadWrite** [AWS managed policy](reference_available-policies.md)\.

**To create a secret \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose **Store a new secret**\.

1. On the **Choose secret type** page, do the following:

   1. For **Secret type**, choose the type of database credentials to store:
      + **Amazon RDS database** \(includes Aurora\)
      + **Amazon DocumentDB database**
      + **Amazon Redshift cluster**

   1. For **Credentials**, enter the credentials for the database\.

   1. For **Encryption key**, choose the AWS KMS key that Secrets Manager uses to encrypt the secret value:
      + For most cases, choose **aws/secretsmanager** to use the AWS managed key for Secrets Manager\. There is no cost for using this key\.
      + If you need to access the secret from another AWS account, or if you want to use your own KMS key so that you can rotate it or apply a key policy to it, choose a customer managed key from the list or choose **Add new key** to create one\. For information about the costs of using a customer managed key, see [Pricing](intro.md#asm_pricing)\.

        You must have the following permissions to the key: `kms:Encrypt`, `kms:Decrypt`, and `kms:GenerateDataKey`\. For more information about cross\-account access, see [Permissions to AWS Secrets Manager secrets for users in a different account](auth-and-access_examples_cross.md)\. 

   1. For **Database**, choose your database\.

   1. Choose **Next**\.

1. On the **Configure secret** page, do the following:

   1. Enter a descriptive **Secret name** and **Description**\. Secret names must contain 1\-512 Unicode characters\.

   1. \(Optional\) In the **Tags** section, add tags to your secret\. For tagging strategies, see [Tag AWS Secrets Manager secrets](managing-secrets_tagging.md)\. Don't store sensitive information in tags because they aren't encrypted\.

   1. \(Optional\) In **Resource permissions**, to add a resource policy to your secret, choose **Edit permissions**\. For more information, see [Attach a permissions policy to an AWS Secrets Manager secret](auth-and-access_resource-policies.md)\.

   1. \(Optional\) In **Replicate secret**, to replicate your secret to another AWS Region, choose **Replicate secret**\. You can replicate your secret now or come back and replicate it later\. For more information, see [Replicate a secret to other Regions](create-manage-multi-region-secrets.md)\.

   1. Choose **Next**\.

1. \(Optional\) On the **Configure rotation** page, you can turn on automatic rotation\. You can also keep rotation off for now and then turn it on later\. For more information, see [Rotate secrets](rotating-secrets.md)\. Choose **Next**\.

1. On the **Review** page, review your secret details, and then choose **Store**\.

   Secrets Manager returns to the list of secrets\. If your new secret doesn't appear, choose the refresh button\.

## AWS CLI<a name="create_database_secret_cli"></a>

When you enter commands in a command shell, there is a risk of the command history being accessed or utilities having access to your command parameters\. See [Mitigate the risks of using the AWS CLI to store your AWS Secrets Manager secrets](security_cli-exposure-risks.md)\.

**Example Create a secret from credentials in a JSON file**  
The following [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/create-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/create-secret.html) example creates a secret from credentials in a file\. For more information, see [Loading AWS CLI parameters from a file](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-parameters-file.html) in the AWS CLI User Guide\.  
For Secrets Manager to be able to rotate the secret, you must make sure the JSON matches the [JSON structure of a secret](reference_secret_json_structure.md)\.  

```
aws secretsmanager create-secret \
    --name MyTestSecret \
    --secret-string file://mycreds.json
```
Contents of mycreds\.json:  

```
{
    "engine": "mysql",
    "username": "saanvis",
    "password": "EXAMPLE-PASSWORD",
    "host": "my-database-endpoint.us-west-2.rds.amazonaws.com",
    "dbname": "myDatabase",
    "port": "3306"
}
```

## AWS SDK<a name="create_database_secret_sdk"></a>

To create a secret by using one of the AWS SDKs, use the [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CreateSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CreateSecret.html) action\. For more information, see [AWS SDKs](asm_access.md#asm-sdks)\.