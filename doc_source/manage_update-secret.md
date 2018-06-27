# Modifying a Secret<a name="manage_update-secret"></a>

You can modify some elements of a secret after you create it\. In the console, you can edit the description, edit or attach a new resource\-based policy to modify permissions to the secret, change the AWS KMS customer master key \(CMK\) that's used to encrypt and decrypt the protected secret information, and edit or add or remove tags\.

You can also change the value of the encrypted secret information\. However, we recommend that you use rotation to update secret values that contain credentials\. Rotation doesn't just update the secret\. It also modifies the credentials on the protected database or service to match those in the secret\. This keeps them automatically synchronized so that when clients request a secret value, they always retrieve a working set of credentials\.

This section includes procedures and commands that describe how to modify the following elements of a secret:
+ [Encrypted secret value](#proc-encrypted-secret-value)
+ [Description](#proc-description)
+ [AWS KMS encryption key](#proc-kmskey)
+ [Tags](#proc-tags)
+ [Permission policy](manage_secret-policy.md)<a name="proc-encrypted-secret-value"></a>

**To modify the encrypted secret value stored in a secret**  
Follow the steps under one of the following tabs:

**Important**  
Updating the secret in this manner doesn't change the credentials on the protected server\. If you want the credentials on the server to stay in sync with the credentials stored in the secret value, we recommend that you enable rotation\. An AWS Lambda function changes both the credentials on the server and those in the secret to match, and tests that the updated credentials work\. For more information, see [Rotating Your AWS Secrets Manager Secrets](rotating-secrets.md)\.

------
#### [ Using the console ]<a name="proc-encrypted-secret-value-console"></a>

When you update the encrypted secret value in a secret, you create a new version of the secret\. The new version automatically gets the staging label `AWSCURRENT` moved to it\. The old version is still accessible by querying for the staging label `AWSPREVIOUS`\. This isn't the same as rotation\.

**Note**  
Any time the staging label `AWSCURRENT` moves from one version to another, Secrets Manager automatically moves the staging label `AWSPREVIOUS` to the version that `AWSCURRENT` was just removed from\.

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the list of secrets, choose the name of the secret with the secret value that you want to modify\.

1. In the **Secret value** section, choose **Retrieve secret value**\.

1. With the secret value now displayed, choose `Edit`\.

1. Update the values as appropriate, and then choose **Save**\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-encrypted-secret-value-api"></a>

You can use the following commands to update the encrypted secret value that's stored in the secret\. When you update the encrypted secret value in a secret, you create a new version of the secret\.

**Note**  
`UpdateSecret` automatically moves the staging label `AWSCURRENT` to the new version of the secret\.   
`PutSecretValue` *does not* automatically move staging labels\. However, it does add `AWSCURRENT` if this command creates the first version of the secret\. Otherwise, it only attaches or moves those labels that you explicitly request with the `VersionStages` parameter\.  
Any time the staging label `AWSCURRENT` moves from one version to another, Secrets Manager automatically moves the staging label `AWSPREVIOUS` to the version that `AWSCURRENT` was just removed from\.
+ **API/SDK:** [http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret), [http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutSecretValue.html](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutSecretValue.html)
+ **AWS CLI:** [http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html](http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html), [http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/put-secret-value.html](http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/put-secret-value.html)

**Example**  
The following example AWS CLI command changes the secret value for a secret\. This results in the creation of a new version\. Secrets Manager automatically moves the `AWSCURRENT` staging label to the new version\. Also, the `AWSPREVIOUS` staging label is automatically moved to the older version that `AWSCURRENT` was just removed from\.  

```
$ aws secretsmanager update-secret --secret-id production/MyAwesomeAppSecret --secret-string '{"username":"anika","password":"a different password"}'
{
    "SecretARN": "arn:aws:secretsmanager:us-west-2:123456789012:secret:production/MyAwesomeAppSecret-AbCdEf",
    "SecretName": "production/MyAwesomeAppSecret",
    "SecretVersionId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE"
}
```
The `SecretVersionId` in the output is the unique secret version ID of the new version of the secret\. You can manually provide the value by using the `ClientRequestToken` parameter\. If you don't specify the value, the SDK or AWS CLI generates a random UUID value for you\.

------<a name="proc-description"></a>

**To modify the description of a secret**  
Follow the steps under one of the following tabs:

------
#### [ Using the console ]<a name="proc-description-console"></a>

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the list of secrets, choose the name of the secret that you want to modify\.

1. In the **Secrets details** section, choose **Actions**, and then choose **Edit description**\.

1. Type a new description or edit the existing text, and then choose **Save**\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-description-api"></a>

You can use the following commands to modify the description of a secret in AWS Secrets Manager:
+ **API/SDK:** [http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html)
+ **AWS CLI:** [http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html](http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html)

**Example**  
The following example AWS CLI command adds or replaces the description with the one provided by the `--description` parameter\.  

```
$ aws secretsmanager update-secret --secret-id production/MyAwesomeAppSecret --description 'This is the description I want to attach to the secret.'
{
    "SecretARN": "arn:aws:secretsmanager:region:accountid:secret:production/MyAwesomeAppSecret-AbCdEf",
    "SecretName": "production/MyAwesomeAppSecret",
    "SecretVersionId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE"
}
```

------<a name="proc-kmskey"></a>

**To modify the AWS KMS encryption key used by a secret**  
Follow the steps under one of the following tabs:

------
#### [ Using the console ]<a name="proc-kmskey-console"></a>

**Important**  
If you change the encryption key that's used by a secret, old versions of the secret are no longer accessible\. This is because the new key can't decrypt secret values for the older versions\.
If you change the encryption key that's used by a secret, you must update the secret value \(with UpdateSecret or PutSecretValue\) at least once before you disable or delete the first CMK\. Updating the secret value decrypts it using the old CMK and reencrypts it with the new CMK\. If you disable or delete the first CMK before this update, the key cannot be decrypted and you lose the contents of the secret unless you can re\-enable the CMK\.

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the list of secrets, choose the name of the secret that you want to modify\.

1. In the **Secrets details** section, choose **Actions**, and then choose **Edit encryption key**\.

1. Choose the AWS KMS encryption key that you want to use to encrypt and decrypt later versions of your secret\. Then choose **Save**\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-kmskey-api"></a>

**Important**  
If you change the encryption key that's used by a secret, old versions of the secret are no longer accessible\. This is because the new key can't decrypt secret values for the older versions\.
If you change the encryption key that's used by a secret, you must update the secret value \(with UpdateSecret or PutSecretValue\) at least once before you disable or delete the first CMK\. Updating the secret value decrypts it using the old CMK and reencrypts it with the new CMK\. If you disable or delete the first CMK before this update, the key cannot be decrypted and you lose the contents of the secret unless you can re\-enable the CMK\.

You can use the following commands to modify the AWS KMS encryption key that's used by the secret\. You must specify the CMK by its Amazon Resource Name \(ARN\)\.
+ **API/SDK:** [http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html)
+ **AWS CLI:** [http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html](http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html)

**Example**  
The following example AWS CLI command adds or replaces the AWS KMS CMK that's used for all encryption and decryption operations in this secret from this time on\.  

```
$ aws secretsmanager update-secret --secret-id production/MyAwesomeAppSecret --kms-key-id arn:aws:kms:region:123456789012:key/EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE
```

------<a name="proc-tags"></a>

**To modify the tags attached to a secret**  
Follow the steps under one of the following tabs:

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-tags-api"></a>

You can use the following commands to add or remove the tags that are attached to a secret in AWS Secrets Manager\. Key names and values are case sensitive\. Only one tag on a secret can have a given key name\. To edit an existing tag, add a tag with the same key name\. It doesn't add a new key\-value pair\. Instead, it updates the value in the existing pair\. To change a key name, you must remove the first key and add a second with the new name\.
+ **API/SDK:** [http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_TagResource](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_TagResource), [http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UntagResource.html](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UntagResource.html)
+ **AWS CLI:** [http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/tag-resource.html](http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/tag-resource.html), [http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/untag-resource.html](http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/untag-resource.html)

**Example**  
The following example AWS CLI command adds or replaces the tags with those provided by the `--tags` parameter\. The parameter is expected to be a JSON array of `Key` and `Value` elements:  

```
$ aws secretsmanager tag-resource --secret-id MySecret2 --tags '[{"Key":"costcenter","Value":"12345"},{"Key":"environment","Value":"production"}]'
```
The tag\-resource command doesn't return any output\. 

**Example**  
The following example AWS CLI command removes the tags with the key "environment" from the specified secret:  

```
$ aws secretsmanager untag-resource --secret-id MySecret2 --tag-keys 'environment'
```
The tag\-resource command doesn't return any output\. 

------