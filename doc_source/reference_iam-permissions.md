# Permissions reference for Secrets Manager<a name="reference_iam-permissions"></a>

To see the elements that make up a permissions policy, see [JSON policy document structure](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html#policies-introduction) and [IAM JSON policy elements reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html)\. 

To get started writing your own permissions policy, see [Permissions policy examples](auth-and-access_examples.md)\.

## Secrets Manager actions<a name="reference_iam-permissions_actions"></a>

The following table shows the actions and related permissions for Secrets Manager\. For a description of each condition key, see [Condition keys](#iam-contextkeys)\.


| Action and permission | Description | Access level | Condition keys | 
| --- | --- | --- | --- | 
|  [ CancelRotateSecret ](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CancelRotateSecret.html) `secretsmanager:CancelRotateSecret` | Enables the user to cancel an in\-progress secret rotation\. | Write |   secretsmanager:SecretId   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:ResourceTag/tag\-key   | 
| [ CreateSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CreateSecret.html) `secretsmanager:CreateSecret` | Enables the user to create a secret that stores encrypted data that can be queried and rotated\. | Write |   secretsmanager:Name   secretsmanager:Description   secretsmanager:KmsKeyId   aws:RequestTag/tag\-key   aws:TagKeys   secretsmanager:ResourceTag/tag\-key   | 
| [ DeleteResourcePolicy](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DeleteResourcePolicy.html) `secretsmanager:DeleteResourcePolicy` | Enables the user to delete the resource policy attached to a secret\. | Permissions management |   secretsmanager:SecretId   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:ResourceTag/tag\-key   | 
| [ DeleteSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DeleteSecret.html) `secretsmanager:DeleteSecret` | Enables the user to delete a secret\. | Write |   secretsmanager:SecretId   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:RecoveryWindowInDays   secretsmanager:ForceDeleteWithoutRecovery   secretsmanager:ResourceTag/tag\-key   | 
| [ DescribeSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DescribeSecret.html) `secretsmanager:DescribeSecret` | Enables the user to retrieve the metadata about a secret, but not the encrypted data\. | Read |   secretsmanager:SecretId   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:ResourceTag/tag\-key   | 
| [ GetRandomPassword](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetRandomPassword.html) `secretsmanager:GetRandomPassword` | Enables the user to generate a random string for use in password creation\. | Read |  | 
| [ GetResourcePolicy](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetResourcePolicy.html) `secretsmanager:GetResourcePolicy` | Enables the user to get the resource policy attached to a secret\. | Read |   secretsmanager:SecretId   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:ResourceTag/tag\-key   | 
| [ GetSecretValue](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html) `secretsmanager:GetSecretValue` | Enables the user to retrieve and decrypt the encrypted data\. | Read |   secretsmanager:SecretId   secretsmanager:VersionId   secretsmanager:VersionStage   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:ResourceTag/tag\-key   | 
| [ ListSecretVersionIds](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ListSecretVersionIds) `secretsmanager:ListSecretVersionIds` | Enables the user to list the available versions of a secret\. | Read |   secretsmanager:SecretId   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:ResourceTag/tag\-key   | 
| [ ListSecrets](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ListSecrets.html) `secretsmanager:ListSecrets` | Enables the user to list the available secrets\. | List |  | 
| [ PutResourcePolicy](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutResourcePolicy.html) `secretsmanager:PutResourcePolicy` | Enables the user to attach a resource policy to a secret\. | Permissions management |   secretsmanager:SecretId   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:ResourceTag/tag\-key   secretsmanager:BlockPublicPolicy   | 
| [ PutSecretValue](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutSecretValue.html) `secretsmanager:PutSecretValue` | Enables the user to create a new version of the secret with new encrypted data\. | Write |   secretsmanager:SecretId   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:ResourceTag/tag\-key   | 
| [ RemoveRegionsFromReplication](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RemoveRegionsFromReplication.html) `secretsmanager:RemoveRegionsFromReplication` | Remove regions from replication\. | Write |   secretsmanager:SecretId   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:ResourceTag/tag\-key   | 
| [ ReplicateSecretToRegions](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ReplicateSecretToRegions.html) `secretsmanager:ReplicateSecretToRegions` | Converts an existing secret to a multi\-Region secret and begins replicating the secret to a list of new regions\. | Write |   secretsmanager:SecretId   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:ResourceTag/tag\-key   | 
| [ RestoreSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RestoreSecret.html) `secretsmanager:RestoreSecret` | Enables the user to cancel deletion of a secret\. | Write |   secretsmanager:SecretId   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:ResourceTag/tag\-key   | 
| [ RotateSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RotateSecret.html) `secretsmanager:RotateSecret` | Enables the user to start rotation of a secret\. | Write |   secretsmanager:SecretId   secretsmanager:RotationLambdaARN   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:ResourceTag/tag\-key   | 
| [ StopReplicationToReplica](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_StopReplicationToReplica.html) `secretsmanager:StopReplicationToReplica` | Removes the secret from replication and promotes the secret to a regional secret in the replica Region\. | Write |   secretsmanager:SecretId   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:ResourceTag/tag\-key   | 
| [ TagResource](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_TagResource.html) `secretsmanager:TagResource` | Enables the user to add tags to a secret\. | Tagging |   secretsmanager:SecretId   aws:RequestTag/tag\-key   aws:TagKeys   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:ResourceTag/tag\-key   | 
| [ UntagResource](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UntagResource.html) `secretsmanager:UntagResource` | Enables the user to remove tags from a secret\. | Tagging |   secretsmanager:SecretId   aws:TagKeys   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:ResourceTag/tag\-key   | 
| [ UpdateSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html) `secretsmanager:UpdateSecret` | Enables the user to update a secret with new metadata or with a new version of the encrypted data\. | Write |   secretsmanager:SecretId   secretsmanager:Description   secretsmanager:KmsKeyId   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:ResourceTag/tag\-key   | 
| [ UpdateSecretVersionStage](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecretVersionStage.html)  secretsmanager:UpdateSecretVersionStage  | Enables the user to move a stage from one secret to another\. | Write |   secretsmanager:SecretId   secretsmanager:VersionStage   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:ResourceTag/tag\-key   | 
| [ ValidateResourcePolicy](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ValidateResourcePolicy.html) `secretsmanager:ValidateResourcePolicy` | Enables the user to validate a resource policy before attaching policy\. | Permissions management |   secretsmanager:SecretId   secretsmanager:resource/AllowRotationLambdaArn   secretsmanager:ResourceTag/tag\-key   | 

## Secrets Manager resources<a name="iam-resources"></a><a name="iam-resources-secret"></a>


****  

| Resource type | ARN format | 
| --- | --- | 
|  Secret  |  arn:aws:secretsmanager:*<Region>*:*<AccountId>*:secret:*SecretName*\-*6RandomCharacters*  | 

Secrets Manager constructs the last part of the ARN by appending a dash and six random alphanumeric characters at the end of the secret name\. If you delete a secret and then recreate another with the same name, this formatting helps ensure that individuals with permissions to the original secret don't automatically get access to the new secret because Secrets Manager generates six new random characters\.

You can find the ARN for a secret in the Secrets Manager console on the secret details page or by calling [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DescribeSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DescribeSecret.html)\.

## Condition keys<a name="iam-contextkeys"></a>

Condition keys in Secrets Manager correspond to the request parameters of an API call\. You can use them to allow or block requests based on the parameter value\. For more information, see [IAM JSON policy elements: Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html)\.

You can also use [AWS global condition context keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html)\.

The following table shows the condition keys for Secrets Manager\.


****  

|  Condition key  | Description | Type | 
| --- | --- | --- | 
|  aws:RequestTag/tag\-key  | Filters access by a key present in the request that the user makes to the Secrets Manager\.   | String | 
|  aws:TagKeys  | Filters access by the list of all the tag key namespresent in the request the user makes to the Secrets Manager service\. | String | 
| secretsmanager:resource/AllowRotationLambdaArn  | Filters the request based on the ARN of the Lambda rotation function attached to the target resource of the request\. This enables you to restrict access to only those secrets with a rotation Lambda ARN matching this value\. Secrets without rotation enabled or with a different rotation Lambda ARN do not match\. | ARN | 
|  secretsmanager:BlockPublicPolicy  | Evaluates your resource policy assigned to a secret and blocks any policies that allow public access\. | Boolean | 
|  secretsmanager:Description  |  Filters the request based on the Description parameter in the request\.  | String | 
|  secretsmanager:ForceDeleteWithoutRecovery  | Filters the request based if the delete specifies no recovery window\. This enables you to effectively disable this feature\. | Boolean | 
|  secretsmanager:KmsKeyId  |  Filters the request based on the KmsKeyId parameter of the request\. This enables you to limit which keys can be used in a request\.  | String | 
|  secretsmanager:Name  |  Filters the request based on Name parameter value of the request\. This enables you to restrict a secret name to only those matching this value\.  | String | 
|  secretsmanager:RecoveryWindowInDays  | Filters the request based on the recovery window specified\. Enables you to enforce that recovery windows occur an approved number of days\. | Long | 
|  secretsmanager:ResourceTag/<tag\-key>  | Filters the request based on a tag attached to the secret\. Replace <tag\-key> with the actual tag name\. You can then use condition operators to ensure the presence of the tag, and contains the requested value\. | String | 
|  secretsmanager:RotationLambdaArn  |  Filters the request based on the `RotationLambdaARN` parameter\. This enables you to restrict which Lambda rotation functions can be used with a secret\. The key can be used with both CreateSecret and the operations modifying existing secrets\.  | ARN | 
|  secretsmanager:SecretId  |  Filters the request based on the ARN for the secret provided in the SecretId parameter\. This enables you to limit which secrets can be accessed by a request\.  | ARN | 
|  secretsmanager:VersionId  |  Filters the request based on the VersionId parameter of the request\. This enables you to restrict which versions of a secret can be accessed\.   | String | 
|  secretsmanager:VersionStage  |  Filters the request based on the staging labels identified in the `VersionStage` parameter of a request\. We recommend you do not use this key, because if you use this key, requests must pass in a staging label to compare to this policy\.  | String | 

## IP address conditions<a name="iam-contextkeys-ipaddress"></a>

Use caution when you specify the [IP address condition operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html#Conditions_IPAddress) or the `aws:SourceIp` condition key in a policy statement that allows or denies access to Secrets Manager\. For example, if you attach a policy that restricts AWS actions to requests from your corporate network IP address range to a secret, then your requests as an IAM user invoking the request from the corporate network work as expected\. However, if you enable other services to access the secret on your behalf, such as when you enable rotation with a Lambda function, that function calls the Secrets Manager operations from an AWS\-internal address space\. Requests impacted by the policy with the IP address filter fail\.

Also, the `aws:sourceIP` condition key is less effective when the request comes from an Amazon VPC endpoint\. To restrict requests to a specific VPC endpoint, use [VPC endpoint conditions](#iam-contextkeys-vpcendpoint)\.

## VPC endpoint conditions<a name="iam-contextkeys-vpcendpoint"></a>

To allow or deny access to requests from a particular VPC or VPC endpoint, use `aws:SourceVpc` to limit access to requests from the specified VPC or `aws:SourceVpce` to limit access to requests from the specified VPC endpoint\. See [Example: Permissions and VPCs](auth-and-access_examples.md#auth-and-access_examples_vpc)\.
+ \.
+ `aws:SourceVpce` limits access to requests from the specified VPC endpoint\.

If you use these condition keys in a resource policy statement that allows or denies access to Secrets Manager secrets, you can inadvertently deny access to services that use Secrets Manager to access secrets on your behalf\. Only some AWS services can run with an endpoint within your VPC\. If you restrict requests for a secret to a VPC or VPC endpoint, then calls to Secrets Manager from a service not configured for the service can fail\.

See [Using Secrets Manager with VPC endpoints](vpc-endpoint-overview.md)\.