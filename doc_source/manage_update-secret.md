# Modifying a Secret<a name="manage_update-secret"></a>

You can modify some elements of a secret after you create it\. In the console, you can edit the description, edit or attach a new resource\-based policy to modify permissions to the secret, change the KMS CMK used to encrypt and decrypt the protected secret information, and edit or add or remove tags\.

You can also change the value of the encrypted secret information, although we recommend that you use rotation to update secret values that contain credentials\. Rotation doesn't just update the secret, it also modifies the credentials on the protected database or service to match those in the secret, keeping them automatically synchronized so that when clients request a secret value they always retrieve a working set of credentials\.

This section includes procedures and commands that describe how to modify the following elements of a secret:
+ [Encrypted secret value](#proc-encrypted-secret-value)
+ [Description](#proc-description)
+ [KMS encryption key](#proc-kmskey)
+ [Tags](#proc-tags)<a name="proc-encrypted-secret-value"></a>

**To modify the encrypted secret value stored in a secret**  
Follow the steps under one of the following tabs:

**Important**  
Updating the secret in this manner does not change the credentials on the protected server\. If you want the credentials on the server to stay in sync with the credentials stored in the secret value, then we recommend that you enable rotation\. An AWS Lambda function changes both the credentials on the server and those in the secret to match and tests that the updated credentials work\. For more information, see [Rotating Your AWS Secrets Manager Secrets](rotating-secrets.md)\.

------
#### [ Using the console ]<a name="proc-encrypted-secret-value-console"></a>

When you update the encrypted secret value in a secret, you create a new version of the secret\. The new version automatically gets the staging label `AWSCURRENT` moved to it\. The old version is still accessible by querying for the staging label `AWSPREVIOUS`\. This is not the same as rotation\.

**Note**  
Any time the staging label `AWSCURRENT` moves from one version to another, Secrets Manager automatically moves the staging label `AWSPREVIOUS` to the version that `AWSCURRENT` was just removed from\.

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the list of secrets, choose the name of the secret with the secret value that you want to modify\.

1. In the **Secret value** section, choose **Retrieve secret value**\.

1. With the secret value now displayed, choose `Edit`\.

1. Update the values as appropriate, and then choose **Save**\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-encrypted-secret-value-api"></a>

You can use the following commands to update the encrypted secret value stored in the secret\. When you update the encrypted secret value in a secret, you create a new version of the secret\.

**Note**  
`UpdateSecret` automatically moves the staging label `AWSCURRENT` to the new version of the secret\.   
`PutSecretValue` *does not* automatically move staging labels, although it does add `AWSCURRENT` if this command creates the first version of the secret\. It otherwise only attaches or moves those labels that you explicitly request with the `VersionStages` parameter\.  
Any time the staging label `AWSCURRENT` moves from one version to another, Secrets Manager automatically moves the staging label `AWSPREVIOUS` to the version that `AWSCURRENT` was just removed from\.
+ **API/SDK:** [http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret), [http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutSecretValue.html](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutSecretValue.html)
+ **CLI:** [http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html](http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html), [http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/put-secret-value.html](http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/put-secret-value.html)

**Example**  
The following example CLI command changes the secret value for a secret\. This results in the creation of a new version, to which Secrets Manager automatically moves the `AWSCURRENT` staging label\. Also, the `AWSPREVIOUS` staging label is automatically moved to the older version that `AWSCURRENT` was just removed from\.  

```
$ aws secretsmanager update-secret --secret-id production/MyAwesomeAppSecret --secret-string '{"username":"anika","password":"a different password"}'
{
    "SecretARN": "arn:aws:secretsmanager:us-west-2:123456789012:secret:production/MyAwesomeAppSecret-AbCdEf",
    "SecretName": "production/MyAwesomeAppSecret",
    "SecretVersionId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE"
}
```
The `SecretVersionId` in the output is the unique secret version ID of the new version of the secret\. You can manually provide the value by using the `ClientRequestToken` parameter\. If you do not specify, the SDK or CLI generates a random UUID value for you\.

------<a name="proc-description"></a>

**To modify the description of a secret**  
Follow the steps under one of the following tabs:

------
#### [ Using the console ]<a name="proc-description-console"></a>

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the list of secrets, choose the name of the secret that you want to modify\.

1. In the **Secrets details** section, choose **Actions**, and then choose **Edit description**\.

1. Type a new description or edit the existing text, and then choose **Save**\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-description-api"></a>

You can use the following commands to modify the description of a secret in AWS Secrets Manager:
+ **API/SDK:** [http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html)
+ **CLI:** [http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html](http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html)

**Example**  
The following example CLI command adds or replaces the description with the one provided by the `--description` parameter\.  

```
$ aws secretsmanager update-secret --secret-id production/MyAwesomeAppSecret --description 'This is the description I want to attach to the secret.'
{
    "SecretARN": "arn:aws:secretsmanager:region:accountid:secret:production/MyAwesomeAppSecret-AbCdEf",
    "SecretName": "production/MyAwesomeAppSecret",
    "SecretVersionId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE"
}
```

------<a name="proc-kmskey"></a>

**To modify the KMS encryption key used by a secret**  
Follow the steps under one of the following tabs:

------
#### [ Using the console ]<a name="proc-kmskey-console"></a>

**Important**  
If you change the encryption key used by a secret, old versions of the secret are no longer accessible because the new key cannot decrypt the older versions' secret value\.

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the list of secrets, choose the name of the secret that you want to modify\.

1. In the **Secrets details** section, choose **Actions**, and then choose **Edit encryption key**\.

1. Choose the KMS encryption key that you want to use to encrypt and decrypt later versions of your secret, and then choose **Save**\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-kmskey-api"></a>

You can use the following commands to modify the KMS encryption key used by the secret\. You must specify the KMS CMK by its ARN\.
+ **API/SDK:** [http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html)
+ **CLI:** [http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html](http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html)

**Example**  
The following example CLI command adds or replaces the KMS CMK used for all encryption and decryption operations in this secret from this time on\.  

```
$ aws secretsmanager update-secret --secret-id production/MyAwesomeAppSecret --kms-key-id arn:aws:kms:region:123456789012:key/EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE
```

------<a name="proc-tags"></a>

**To modify the tags attached to a secret**  
Follow the steps under one of the following tabs:

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-tags-api"></a>

You can use the following commands to add or remove the tags attached to a secret in AWS Secrets Manager\. Key names and values are case sensitive\. Only one tag on a secret can have a given key name\. To edit an existing tag, add a tag with the same key name\. It does not add a new key/value pair, it instead updates the value in the existing pair\. To change a key name, you must remove the first key and add a second with the new name\.
+ **API/SDK:** [http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_TagResource](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_TagResource), [http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UntagResource.html](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UntagResource.html)
+ **CLI:** [http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/tag-resource.html](http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/tag-resource.html), [http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/untag-resource.html](http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/untag-resource.html)

**Example**  
The following example CLI command adds or replaces the tags with those provided by the `--tags` parameter\. The parameter is expected to be a JSON array of `Key` and `Value` elements:  

```
$ aws secretsmanager tag-resource --secret-id MySecret2 --tags '[{"Key":"costcenter","Value":"12345"},{"Key":"environment","Value":"production"}]'
```
The tag\-resource command does not return any output\. 

**Example**  
The following example CLI command removes the tags with the key "environment" from the specified secret:  

```
$ aws secretsmanager untag-resource --secret-id MySecret2 --tag-keys 'environment'
```
The tag\-resource command does not return any output\. 

------