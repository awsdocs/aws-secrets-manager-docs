# Modify an AWS Secrets Manager secret<a name="manage_update-secret"></a>

You can modify the metadata of a secret after it is created, depending on who created the secret\. For secrets created by other services, you might need to use the other service to update or rotate it\. 

To determine who manages a secret, you can review the secret name\. Secrets managed by other services are prefixed with the ID of that service\. Or, in the AWS CLI, call [describe\-secret](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/describe-secret.html), and then review the field `OwningService`\. For more information, see [AWS Secrets Manager secrets managed by other AWS services](service-linked-secrets.md)\.

For secrets you manage, you can modify the description, resource\-based policy, the encryption key, and tags\. You can also change the encrypted secret value; however, we recommend you use rotation to update secret values that contain credentials\. Rotation updates both the secret in Secrets Manager and the credentials on the database or service\. This keeps the secret automatically synchronized so when clients request a secret value, they always get a working set of credentials\. For more information, see [Rotate AWS Secrets Manager secrets](rotating-secrets.md)\.

**To update a secret you manage \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. From the list of secrets, choose your secret\.

1. On the secret details page, do any of the following:

   **Note** that you can't change the name or ARN of a secret\. 
   + To update the description, in the **Secrets details** section, choose **Actions**, and then choose **Edit description**\.
   + To update the encryption key, in the **Secrets details** section, choose **Actions**, and then choose **Edit encryption key**\. See [Secret encryption and decryption in AWS Secrets Manager](security-encryption.md)\.
   + To update tags, in the **Tags** section, choose **Edit**\. See [Tag AWS Secrets Manager secrets](managing-secrets_tagging.md)\.
   + To update the secret value, in the **Secret value** section, choose **Retrieve secret value** and then choose **Edit**\. 

     Secrets Manager creates a new version of the secret with the staging label `AWSCURRENT`\. You can still access the old version\. From the CLI, use the [get\-secret\-value](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html) action with `version-id` `AWSPREVIOUS`\. 
   + To update rotation for your secret, choose Edit rotation\. See [Rotate AWS Secrets Manager secrets](rotating-secrets.md)\.
   + To update permissions for your secret, choose Edit permissions\. See [Attach a permissions policy to an AWS Secrets Manager secret](auth-and-access_resource-policies.md)\.
   + To replicate your secret to other Regions, see [Replicate a secret to other Regions](create-manage-multi-region-secrets.md)\.
   + If your secret has replicas, you can change the encryption key for a replica\. In the **Replicate secret** section, select the radio button for the replica, and then on the **Actions** menu, choose **Edit encryption key**\. See [Secret encryption and decryption in AWS Secrets Manager](security-encryption.md)\.

## AWS CLI<a name="manage_update-secret_CLI"></a>

**Example Update secret description**  
The following [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html) example updates the description of a secret\.  

```
aws secretsmanager update-secret \
    --secret-id MyTestSecret \
    --description "This is a new description for the secret."
```

**Example Update the encryption key associated with a secret**  
The following [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html) example updates the KMS key used to encrypt the secret value\. The KMS key must be in the same region as the secret\.  

```
aws secretsmanager update-secret \
    --secret-id MyTestSecret \
    --kms-key-id arn:aws:kms:us-west-2:123456789012:key/EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE
```

**Example Store a new secret value in a secret**  
When you enter commands in a command shell, there is a risk of the command history being accessed or utilities having access to your command parameters\. See [Mitigate the risks of using the AWS CLI to store your AWS Secrets Manager secrets](security_cli-exposure-risks.md)\.  
The following [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/put-secret-value.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/put-secret-value.html) creates a new version of a secret with two key\-value pairs\.  

```
aws secretsmanager put-secret-value \
    --secret-id MyTestSecret \
    --secret-string "{\"user\":\"diegor\",\"password\":\"EXAMPLE-PASSWORD\"}"
```

**Example Store a new secret value from credentials in a JSON file**  
When you enter commands in a command shell, there is a risk of the command history being accessed or utilities having access to your command parameters\. See [Mitigate the risks of using the AWS CLI to store your AWS Secrets Manager secrets](security_cli-exposure-risks.md)\.  
The following [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/put-secret-value.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/put-secret-value.html) example creates a new version of a secret from credentials in a file\. For more information, see [Loading AWS CLI parameters from a file](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-parameters-file.html) in the AWS CLI User Guide\.  

```
aws secretsmanager put-secret-value \
    --secret-id MyTestSecret \
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

## AWS SDK<a name="manage_update-secret_SDK"></a>

We recommend you avoid calling `PutSecretValue` or `UpdateSecret` at a sustained rate of more than once every 10 minutes\. When you call `PutSecretValue` or `UpdateSecret` to update the secret value, Secrets Manager creates a new version of the secret\. Secrets Manager removes outdated versions when there are more than 100, but it does not remove versions created less than 24 hours ago\. If you update the secret value more than once every 10 minutes, you create more versions than Secrets Manager removes, and you will reach the quota for secret versions\.

To update a secret, use the following actions: [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html), [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ReplicateSecretToRegions.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ReplicateSecretToRegions.html), or [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutSecretValue.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutSecretValue.html)\. For more information, see [AWS SDKs](asm_access.md#asm-sdks)\.