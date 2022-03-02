# Modify a secret<a name="manage_update-secret"></a>

You can modify some parts of a secret after you create it: the description, resource\-based policy, the encryption key, and tags\. You can also change the encrypted secret value; however, we recommend you use rotation to update secret values that contain credentials\. Rotation updates both the secret in Secrets Manager and the credentials on the database or service\. This keeps the secret automatically synchronized so when clients request a secret value, they always get a working set of credentials\. For more information, see [Rotate AWS Secrets Manager secrets](rotating-secrets.md)\.

**To update a secret \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. From the list of secrets, choose your secret\.

1. On the secret details page, do any of the following:
   + To update the description, in the **Secrets details** section, choose **Actions**, and then choose **Edit description**\.
   + To update the encryption key, in the **Secrets details** section, choose **Actions**, and then choose **Edit encryption key**\. See [Secret encryption and decryption](security-encryption.md)\.
   + To update tags, in the **Tags** section, choose **Edit**\. See [Tag secrets](managing-secrets_tagging.md)\.
   + To update the secret value, in the **Secret value** section, choose **Retrieve secret value** and then choose **Edit**\. 

     Secrets Manager creates a new version of the secret with the staging label `AWSCURRENT`\. You can still access the old version\. From the CLI, use the [get\-secret\-value](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html) action with `version-id` `AWSPREVIOUS`\. 
   + To update rotation for your secret, choose Edit rotation\. See [Rotate AWS Secrets Manager secrets](rotating-secrets.md)\.
   + To update permissions for your secret, choose Edit permissions\. See [Attach a permissions policy to a secret](auth-and-access_resource-policies.md)\.
   + To replicate your secret to other Regions, see [Replicate a secret to other Regions](create-manage-multi-region-secrets.md)\.
   + If your secret has replicas, you can change the encryption key for a replica\. In the **Replicate secret** section, select the radio button for the replica, and then on the **Actions** menu, choose **Edit encryption key**\. See [Secret encryption and decryption](security-encryption.md)\.

## AWS CLI<a name="manage_update-secret_CLI"></a>

To update a secret by using the AWS CLI, use the [update\-secret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html) or [put\-secret\-value](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutSecretValue.html) operation\. To tag a secret, see [Tag secrets](managing-secrets_tagging.md)\.

**Example: Update secret description**  
The following example adds or replaces the description with the one in the `--description` parameter\.  

```
$ aws secretsmanager update-secret --secret-id production/MyAwesomeAppSecret --description 'This is the description I want to attach to the secret.'
            {
              "ARN": "arn:aws:secretsmanager:us-east-2:111122223333:secret:production/MyAwesomeAppSecret-AbCdEf",
              "Name": "production/MyAwesomeAppSecret",
              "VersionId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE"
              }
```

**Example: Update encryption key**  
The following example adds or replaces the encryption key for this secret\.   
When you change the encryption key, Secrets Manager re\-encrypts versions of the secret that have the staging labels `AWSCURRENT`, `AWSPENDING`, and `AWSPREVIOUS` under the new encryption key\. When the secret value changes, Secrets Manager also encrypts it under the new key\. You can use the old key or the new one to decrypt the secret when you retrieve it\.  

```
$ aws secretsmanager update-secret --secret-id production/MyAwesomeAppSecret --kms-key-id arn:aws:kms:Region:AccountId:key/EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE
```

**Example: Update secret value**  
When you update the secret value for a secret, Secrets Manager creates a new version with the `AWSCURRENT` staging label and moves the `AWSPREVIOUS` staging label to the version that previously had the label `AWSCURRENT`\.  
We recommend you avoid calling `PutSecretValue` or `UpdateSecret` at a sustained rate of more than once every 10 minutes\. When you call `PutSecretValue` or `UpdateSecret` to update the secret value, Secrets Manager creates a new version of the secret\. Secrets Manager removes outdated versions when there are more than 100, but it does not remove versions created less than 24 hours ago\. If you update the secret value more than once every 10 minutes, you create more versions than Secrets Manager removes, and you will reach the quota for secret versions\.  
The following example AWS CLI command updates the secret value for a secret\.   

```
$ aws secretsmanager put-secret-value --secret-id production/MyAwesomeAppSecret --secret-string '{"username":"anika","password":"EXAMPLE-PASSWORD"}'
          {
            "SecretARN": "arn:aws:secretsmanager:us-east-2:123456789012:secret:production/MyAwesomeAppSecret-AbCdEf",
            "SecretName": "production/MyAwesomeAppSecret",
            "VersionId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE"
            }
```

## AWS SDK<a name="manage_update-secret_SDK"></a>

We recommend you avoid calling `PutSecretValue` or `UpdateSecret` at a sustained rate of more than once every 10 minutes\. When you call `PutSecretValue` or `UpdateSecret` to update the secret value, Secrets Manager creates a new version of the secret\. Secrets Manager removes outdated versions when there are more than 100, but it does not remove versions created less than 24 hours ago\. If you update the secret value more than once every 10 minutes, you create more versions than Secrets Manager removes, and you will reach the quota for secret versions\.

To update a secret, use the following actions: [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html), [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ReplicateSecretToRegions.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ReplicateSecretToRegions.html), or [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutSecretValue.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutSecretValue.html)\. For more information, see [AWS SDKs](asm_access.md#asm-sdks)\.