# Actions, Resources, and Context Keys You Can Use in an IAM Policy or Secret Policy for AWS Secrets Manager<a name="reference_iam-permissions"></a>

## Actions That You Can Reference in an IAM Policy or Secret Policy<a name="iam-actions"></a>

The following table shows the permissions that you can specify in an IAM policy or secret policy to control access to your secrets\. Each permission on an "Action" can be associated with a "Resource" that specifies what the action can work on\. 

You can restrict use of some actions to only those secrets with Amazon Resource Names \(ARNs\) that match the `Resource` element in the policy\. See the section [Resources That You Can Reference in an IAM Policy or Secret Policy](#iam-resources) later in this topic\.

In addition to the AWS Secrets Manager\-specific context keys shown in the last column, you also use the [AWS Global Condition Context Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html)\.

If you see an expand arrow \(**↗**\) in the upper\-right corner of the table, you can open the table in a new window\. To close the window, choose the close button \(**X**\) in the lower\-right corner\.


****  

|  Permission for "Action" element  |  API operation that's enabled by this action  |  Resource ARNs that can be used as a "Resource" with this action  |  Secrets Manager Context keys that can be used with this action  | 
| --- | --- | --- | --- | 
|  secretsmanager:CancelRotateSecret  |  [CancelRotateSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CancelRotateSecret.html)  |  [Secret](#iam-resources-secret)  |  [secretsmanager:SecretId](#iam-contextkey-secretid) [secretsmanager:AllowRotationLambdaArn](#iam-contextkey-resource-allowrotationlambdaarn) [secretsmanager:ResourceTag/*<tagname>*](#iam-contextkey-resource-resourcetag)  | 
|  secretsmanager:CreateSecret  |  [CreateSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CreateSecret.html)  |  |  [secretsmanager:Name](#iam-contextkey-name) [secretsmanager:Description](#iam-contextkey-description) [secretsmanager:KmsKeyId](#iam-contextkey-kmskeyid)  | 
| secretsmanager:DeleteResourcePolicy | [DeleteResourcePolicy](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DeleteResourcePolicy.html) | [Secret](#iam-resources-secret) |  [secretsmanager:SecretId](#iam-contextkey-secretid) [secretsmanager:AllowRotationLambdaArn](#iam-contextkey-resource-allowrotationlambdaarn) [secretsmanager:ResourceTag/*<tagname>*](#iam-contextkey-resource-resourcetag)  | 
|  secretsmanager:DeleteSecret  |  [DeleteSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DeleteSecret.html)  |  [Secret](#iam-resources-secret)  |  [secretsmanager:SecretId](#iam-contextkey-secretid) [secretsmanager:AllowRotationLambdaArn](#iam-contextkey-resource-allowrotationlambdaarn) [secretsmanager:ResourceTag/*<tagname>*](#iam-contextkey-resource-resourcetag)  | 
|  secretsmanager:DescribeSecret  |  [DescribeSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DescribeSecret.html)  |  [Secret](#iam-resources-secret)  |  [secretsmanager:SecretId](#iam-contextkey-secretid) [secretsmanager:AllowRotationLambdaArn](#iam-contextkey-resource-allowrotationlambdaarn) [secretsmanager:RecoveryWindowInDays](#iam-contextkey-recoverywindowindays) [secretsmanager:ForceDeleteWithoutRecovery](#iam-contextkey-forcedeletewithoutrecovery) [secretsmanager:ResourceTag/*<tagname>*](#iam-contextkey-resource-resourcetag)  | 
|  secretsmanager:GetRandomPassword  |  [GetRandomPassword](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetRandomPassword.html)  |  |  | 
| secretsmanager:GetResourcePolicy | [GetResourcePolicy](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetResourcePolicy.html) | [Secret](#iam-resources-secret) | [secretsmanager:SecretId](#iam-contextkey-secretid) [secretsmanager:AllowRotationLambdaArn](#iam-contextkey-resource-allowrotationlambdaarn) [secretsmanager:ResourceTag/*<tagname>*](#iam-contextkey-resource-resourcetag)  | 
|  secretsmanager:GetSecretValue  |  [GetSecretValue](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html)  |  [Secret](#iam-resources-secret)  |  [secretsmanager:SecretId](#iam-contextkey-secretid) [secretsmanager:VersionId](#iam-contextkey-versionid) [secretsmanager:VersionStage](#iam-contextkey-versionstage) [secretsmanager:AllowRotationLambdaArn](#iam-contextkey-resource-allowrotationlambdaarn) [secretsmanager:ResourceTag/*<tagname>*](#iam-contextkey-resource-resourcetag)  | 
|  secretsmanager:ListSecrets  |  [ListSecrets](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ListSecrets.html)  |  |  | 
|  secretsmanager:ListSecretVersionIds  |  [ListSecretVersionIds](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ListSecretVersionIds.html)  |  [Secret](#iam-resources-secret)  |  [secretsmanager:SecretId](#iam-contextkey-secretid) [secretsmanager:AllowRotationLambdaArn](#iam-contextkey-resource-allowrotationlambdaarn) [secretsmanager:ResourceTag/*<tagname>*](#iam-contextkey-resource-resourcetag)  | 
|  secretsmanager:PutResourcePolicy  |  [PutResourcePolicy](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutResourcePolicy.html)  |  [Secret](#iam-resources-secret)  |  [secretsmanager:SecretId](#iam-contextkey-secretid) [secretsmanager:AllowRotationLambdaArn](#iam-contextkey-resource-allowrotationlambdaarn) [secretsmanager:ResourceTag/*<tagname>*](#iam-contextkey-resource-resourcetag)  | 
|  secretsmanager:PutSecretValue  |  [PutSecretValue](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutSecretValue.html)  |  [Secret](#iam-resources-secret)  |  [secretsmanager:SecretId](#iam-contextkey-secretid)  | 
|  secretsmanager:RestoreSecret  |  [RestoreSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RestoreSecret.html)  |  [Secret](#iam-resources-secret)  |  [secretsmanager:SecretId](#iam-contextkey-secretid) [secretsmanager:AllowRotationLambdaArn](#iam-contextkey-resource-allowrotationlambdaarn) [secretsmanager:ResourceTag/*<tagname>*](#iam-contextkey-resource-resourcetag)  | 
|  secretsmanager:RotateSecret  |  [RotateSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RotateSecret.html)  |  [Secret](#iam-resources-secret)  |  [secretsmanager:SecretId](#iam-contextkey-secretid) [secretsmanager:RotationLambdaArn](#iam-contextkey-rotationlambdaarn) [secretsmanager:AllowRotationLambdaArn](#iam-contextkey-resource-allowrotationlambdaarn) [secretsmanager:ResourceTag/*<tagname>*](#iam-contextkey-resource-resourcetag)  | 
|  secretsmanager:TagResource  |  [TagResource](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_TagResource.html)  |  [Secret](#iam-resources-secret)  |  [secretsmanager:SecretId](#iam-contextkey-secretid) [secretsmanager:AllowRotationLambdaArn](#iam-contextkey-resource-allowrotationlambdaarn) [secretsmanager:ResourceTag/*<tagname>*](#iam-contextkey-resource-resourcetag)  | 
|  secretsmanager:UntagResource  |  [UntagResource](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UntagResource.html)  |  [Secret](#iam-resources-secret)  |  [secretsmanager:SecretId](#iam-contextkey-secretid) [secretsmanager:AllowRotationLambdaArn](#iam-contextkey-resource-allowrotationlambdaarn) [secretsmanager:ResourceTag/*<tagname>*](#iam-contextkey-resource-resourcetag)  | 
|  secretsmanager:UpdateSecret  |  [UpdateSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html)  |  [Secret](#iam-resources-secret)  |  [secretsmanager:SecretId](#iam-contextkey-secretid) [secretsmanager:Description](#iam-contextkey-description) [secretsmanager:KmsKeyId](#iam-contextkey-kmskeyid) [secretsmanager:AllowRotationLambdaArn](#iam-contextkey-resource-allowrotationlambdaarn) [secretsmanager:ResourceTag/*<tagname>*](#iam-contextkey-resource-resourcetag)  | 
|  secretsmanager:UpdateSecretVersionStage  |  [UpdateSecretVersionStage](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecretVersionStage.html)  |  [Secret](#iam-resources-secret)  |  [secretsmanager:SecretId](#iam-contextkey-secretid) [secretsmanager:VersionStage](#iam-contextkey-versionstage) [secretsmanager:AllowRotationLambdaArn](#iam-contextkey-resource-allowrotationlambdaarn) [secretsmanager:ResourceTag/*<tagname>*](#iam-contextkey-resource-resourcetag)  | 

## Resources That You Can Reference in an IAM Policy or Secret Policy<a name="iam-resources"></a>

The following table shows the ARN formats that are supported in IAM policies for AWS Secrets Manager\. You can view the IDs for each entity on the **Secret details** page for each secret in the Secrets Manager console\.

If you see an expand arrow \(**↗**\) in the upper\-right corner of the table, you can open the table in a new window\. To close the window, choose the close button \(**X**\) in the lower\-right corner\.<a name="iam-resources-secret"></a>


****  

| Resource Type | ARN Format | 
| --- | --- | 
|  Secret |  arn:aws:secretsmanager:*<Region>*:*<AccountId>*:secret:*OptionalPath/**SecretName*\-*6RandomCharacters*  | 

Secrets Manager constructs the last part of the ARN by appending a dash and six random alphanumeric characters at the end of your secret's name\. If you ever delete a secret and then recreate another with the same name, this formatting helps ensure that individuals with permissions to the original secret don't automatically get access to the new secret because the six random characters will be different\.

## Context Keys That You Can Reference in an IAM Policy or Secret Policy<a name="iam-contextkeys"></a>

Context keys in AWS Secrets Manager generally correspond to the request parameters of an API call\. This enables you to allow or block almost any request based on the value of a parameter\.

Each context key can be compared using a condition operator to a value that you specify\. The context keys that can be used depend on the action selected\. See the "Context keys" column in the [Actions](#iam-actions) section at the beginning of this topic\.

For example, you could choose to allow someone to retrieve *only* the `AWSCURRENT` version of a secret value by using a `Condition` element similar to the following:

```
    "Condition": {"ForAnyValue:StringEquals" : {"secretsmanager:VersionStage" : "AWSCURRENT"}}
```

The following table shows the Secrets Manager\-specific context keys that you can specify in the `Condition` element of an IAM permissions policy to more granularly control access to an action\. In addition to the keys below, you can also use the [AWS Global Condition Context Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html)\.


****  

|  Context keys for "Condition" element  | Description | 
| --- | --- | 
|   secretsmanager:Resource/AllowRotationLambdaArn  | Filters the request based on the ARN of the Lambda rotation function attached to the resource that the request is targeting\. This enables you to restrict access to only those secrets that already have a rotation Lambda ARN that matches this value\. Secrets that do not have rotation enabled or that have a different rotation Lambda ARN do not match\. | 
|   secretsmanager:Description  |  Filters the request based on the Description parameter in the request\.  | 
|   secretsmanager:ForceDeleteWithoutRecovery  | Filters the request based on whether the delete specifies no recovery window\. This enables you to effectively disable this feature\. | 
|   secretsmanager:KmsKeyId  |  Filters the request based on the KmsKeyId parameter of the request\. This enables you to limit which keys can be used in a request\.  | 
|   secretsmanager:Name  |  Filters the request based on Name parameter value of the request\. This enables you to restrict a secret's name to only those matching this value\.  | 
|   secretsmanager:RecoveryWindowInDays  | Filters the request based on the recovery window specified\. Enables you to enforce that recovery windows are always an approved number of days\. | 
|   secretsmanager:ResourceTag/*<tagname>*  | Filters the request based on a tag attached to the secret\. Replace <tagname> with the actual tag name\. You can then use condition operators to ensure that the tag is present, and that it has the requested value\. | 
|   secretsmanager:RotationLambdaArn  |  Filters the request based on the `RotationLambdaARN` parameter\. This enables you to restrict which Lambda rotation functions can be used with a secret\. It can be used with both CreateSecret and the operations that modify existing secrets\.  | 
|   secretsmanager:SecretId  |  Filters the request based on the unique identifier for the secret that's provided in the SecretId parameter\. The value can be either the friendly name or the ARN of the secret\. This enables you to limit which secrets can be accessed by a request\.  | 
|   secretsmanager:VersionId  |  Filters the request based on the VersionId parameter of the request\. This enables you to restrict which versions of a secret can be accessed\.   | 
|   secretsmanager:VersionStage  |  Filters the request based on the staging labels that are identified in the VersionStage parameter of a request\. This enables you to restrict access to only the secret versions that have a staging label that matches one of the values in this string array parameter\. Because this is a multi\-valued string array, [you must use one of the set operators to compare strings with this value\.](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_multi-value-conditions.html)  | 

### AWS Global Condition Keys<a name="iam-contextkeys-global"></a>

AWS provides [global condition keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#AvailableKeys), which are a set of predefined condition keys for all AWS services that use IAM for access control\. For example, you can use the `aws:PrincipalType` condition key to allow access only when the principal in the request is the type you specify\.

Secrets Manager supports all global condition keys, including the `aws:TagKeys` and `aws:RequestTag` condition keys that control access based on the resource tag in the request\. These condition keys are supported by some, but not all, AWS services\.

**Topics**
+ [Using the IP Address Condition in Policies with Secrets Manager Permissions](#iam-contextkeys-ipaddress)
+ [Using VPC Endpoint Conditions in Policies with Secrets Manager Permissions](#iam-contextkeys-vpcendpoint)

#### Using the IP Address Condition in Policies with Secrets Manager Permissions<a name="iam-contextkeys-ipaddress"></a>

You can use Secrets Manager to protect your credentials for a database or service\. However, use caution when you specify the [IP address condition operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html#Conditions_IPAddress) or the `aws:SourceIp` condition key in the same policy statement that allows or denies access to Secrets Manager\. For example, the policy in [AWS: Denies Access to AWS Based on the Source IP](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_aws_deny-ip.html) restricts AWS actions to requests from the specified IP range\.

If you attach a similar policy to a secret that enables the secret to be accessed only from your corporate network's IP address range, then your requests as an IAM user invoking the request from the corporate network work fine\. However, if you enable other services to access the secret on your behalf, such as when you enable rotation with a Lambda function, that function calls the Secrets Manager operations from an AWS\-internal address space\. Requests that are impacted by the policy with the IP address filter fail\.

Also, the `aws:sourceIP` condition key isn't effective when the request comes from an [Amazon VPC endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html)\. To restrict requests to a specific VPC endpoint, including a [Secrets Manager VPC endpoint](https://docs.aws.amazon.com/kms/latest/developerguide/kms-vpc-endpoint.html), use the `aws:sourceVpce` or `aws:sourceVpc` condition keys\. For more information, see [VPC Endpoints \- Controlling the Use of Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html#vpc-endpoints-iam-access) in the *Amazon VPC User Guide*\.

#### Using VPC Endpoint Conditions in Policies with Secrets Manager Permissions<a name="iam-contextkeys-vpcendpoint"></a>

[Secrets Manager supports Amazon VPC endpoints](vpc-endpoint-overview.md#vpc-endpoint) that are provided by [AWS PrivateLink](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Introduction.html#what-is-privatelink)\. You can use the following [global condition keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#AvailableKeys) in IAM policies or [resource\-based policies](auth-and-access_resource-based-policies.md) to allow or deny access to requests from a particular VPC or VPC endpoint\.
+ `aws:SourceVpc` limits access to requests from the specified VPC\.
+ `aws:SourceVpce` limits access to requests from the specified VPC endpoint\.

If you use these condition keys in a secret policy statement that allows or denies access to Secrets Manager secrets, you can inadvertently deny access to services that use Secrets Manager to access secrets on your behalf\. Only some AWS services can run with an endpoint within your VPC\. If you restrict requests for a secret to a VPC or VPC endpoint, then calls to Secrets Manager from a service that isn't configured to run in that VPC can fail\.