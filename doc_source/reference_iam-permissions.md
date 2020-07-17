# Actions, Resources, and Context Keys You Can Use in an IAM Policy or Secret Policy for AWS Secrets Manager<a name="reference_iam-permissions"></a>

## Actions You Can Reference in an IAM Policy or Secret Policy<a name="iam-actions"></a>

You can specify the permissions in an IAM policy or secret policy to control access to your secrets\. Each permission on an Action can be associated with a Resource that specifies what the action can work on\. 

For a complete list of actions, see [Actions, Resources, and Condition Keys for AWS Secrets Manager](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awssecretsmanager.html) in the IAM User's Guide\.

## Resources You Can Reference in an IAM Policy or Secret Policy<a name="iam-resources"></a>

The following table shows the ARN formats supported in IAM policies for AWS Secrets Manager\. You can view the IDs for each entity on the **Secret details** page for each secret in the Secrets Manager console\.

If you see an expand arrow \(**↗**\) in the upper\-right corner of the table, you can open the table in a new window\. To close the window, choose the close button \(**X**\) in the lower\-right corner\.<a name="iam-resources-secret"></a>


****  

| Resource Type | ARN Format | 
| --- | --- | 
|  Secret |  arn:aws:secretsmanager:*<Region>*:*<AccountId>*:secret:*OptionalPath/**SecretName*\-*6RandomCharacters*  | 

Secrets Manager constructs the last part of the ARN by appending a dash and six random alphanumeric characters at the end of the secret name\. If you delete a secret and then recreate another with the same name, this formatting helps ensure individuals with permissions to the original secret don't automatically get access to the new secret because Secrets Manager generates six new random characters\.

## Context Keys You Can Reference in an IAM Policy or Secret Policy<a name="iam-contextkeys"></a>

Context keys in AWS Secrets Manager generally correspond to the request parameters of an API call\. This enables you to allow or block any request based on the parameter value\.

Each context key can be compared using a condition operator to a specified value\. The context keys you use depend on the action selected\. See the "Context keys" column in the [Actions](#iam-actions) section at the beginning of this topic\.

For example, you can allow someone to retrieve *only* the `AWSCURRENT` version of a secret value by using a `Condition` element similar to the following:

```
    "Condition": {"ForAnyValue:StringEquals" : {"secretsmanager:VersionStage" : "AWSCURRENT"}}
```

The following table shows the Secrets Manager\-specific context keys you specify in the `Condition` element of an IAM permissions policy to more granularly control access to an action\. In addition to the keys below, you can also use the [AWS Global Condition Context Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html)\.


****  

|  Context keys for "Condition" element  | Description | Type | 
| --- | --- | --- | 
|   aws:RequestTag/tag\-key  | Filters access by a key present in the request that the user makes to the Secrets Manager\.   | String | 
|   aws:TagKeys  | Filters access by the list of all the tag key namespresent in the request the user makes to the Secrets Manager service\. | String | 
|   secretsmanager:resource/AllowRotationLambdaArn  | Filters the request based on the ARN of the Lambda rotation function attached to the target resource of the request\. This enables you to restrict access to only those secrets with a rotation Lambda ARN matching this value\. Secrets without rotation enabled or with a different rotation Lambda ARN do not match\. | ARN | 
|   secretsmanager:Description  |  Filters the request based on the Description parameter in the request\.  | String | 
|   secretsmanager:ForceDeleteWithoutRecovery  | Filters the request based if the delete specifies no recovery window\. This enables you to effectively disable this feature\. | Boolean | 
|   secretsmanager:KmsKeyId  |  Filters the request based on the KmsKeyId parameter of the request\. This enables you to limit which keys can be used in a request\.  | Boolean | 
|   secretsmanager:Name  |  Filters the request based on Name parameter value of the request\. This enables you to restrict a secret name to only those matching this value\.  | String | 
|   secretsmanager:RecoveryWindowInDays  | Filters the request based on the recovery window specified\. Enables you to enforce that recovery windows occur an approved number of days\. | Long | 
|   secretsmanager:ResourceTag/*<tag\-key>*  | Filters the request based on a tag attached to the secret\. Replace <tag\-key> with the actual tag name\. You can then use condition operators to ensure the presence of the tag, and tcontains the requested value\. | String | 
|   secretsmanager:RotationLambdaArn  |  Filters the request based on the `RotationLambdaARN` parameter\. This enables you to restrict which Lambda rotation functions can be used with a secret\. The key can be used with both CreateSecret and the operations modifying existing secrets\.  | ARN | 
|   secretsmanager:SecretId  |  Filters the request based on the unique identifier for the secret provided in the SecretId parameter\. The value can be either the friendly name or the ARN of the secret\. This enables you to limit which secrets can be accessed by a request\.  | ARN | 
|   secretsmanager:VersionId  |  Filters the request based on the VersionId parameter of the request\. This enables you to restrict which versions of a secret can be accessed\.   | String | 
|   secretsmanager:VersionStage  |  Filters the request based on the staging labels identified in the VersionStage parameter of a request\. This enables you to restrict access to only the secret versions with a staging label matching one of the values in this string array parameter\. Because the key usesa multi\-valued string array, [you must use one of the set operators to compare strings with this value\.](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_multi-value-conditions.html)  | String | 

### AWS Global Condition Keys<a name="iam-contextkeys-global"></a>

AWS provides [global condition keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#AvailableKeys), a set of predefined condition keys for all AWS services that use IAM for access control\. For example, you can use the `aws:PrincipalType` condition key to allow access only when the principal in the request contains the type you specify\.

Secrets Manager supports all global condition keys, including the `aws:TagKeys` and `aws:RequestTag` condition keys that control access based on the resource tag in the request\. Some, not all AWS services support these condition keys\.

**Topics**
+ [Using the IP Address Condition in Policies with Secrets Manager Permissions](#iam-contextkeys-ipaddress)
+ [Using VPC Endpoint Conditions in Policies with Secrets Manager Permissions](#iam-contextkeys-vpcendpoint)

#### Using the IP Address Condition in Policies with Secrets Manager Permissions<a name="iam-contextkeys-ipaddress"></a>

You can use Secrets Manager to protect your credentials for a database or service\. However, use caution when you specify the [IP address condition operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html#Conditions_IPAddress) or the `aws:SourceIp` condition key in the same policy statement that allows or denies access to Secrets Manager\. For example, the policy in [AWS: Denies Access to AWS Based on the Source IP](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_aws_deny-ip.html) restricts AWS actions to requests from the specified IP range\.

If you attach a similar policy to a secret enabling the secret for access from your corporate network IP address range, then your requests as an IAM user invoking the request from the corporate network work as expected\. However, if you enable other services to access the secret on your behalf, such as when you enable rotation with a Lambda function, that function calls the Secrets Manager operations from an AWS\-internal address space\. Requests impacted by the policy with the IP address filter fail\.

Also, the `aws:sourceIP` condition key becomes less effective when the request comes from an [Amazon VPC endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html)\. To restrict requests to a specific VPC endpoint, including a [Secrets Manager VPC endpoint](https://docs.aws.amazon.com/kms/latest/developerguide/kms-vpc-endpoint.html), use the `aws:sourceVpce` or `aws:sourceVpc` condition keys\. For more information, see [VPC Endpoints \- Controlling the Use of Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html#vpc-endpoints-iam-access) in the *Amazon VPC User Guide*\.

#### Using VPC Endpoint Conditions in Policies with Secrets Manager Permissions<a name="iam-contextkeys-vpcendpoint"></a>

[Secrets Manager supports Amazon VPC endpoints](vpc-endpoint-overview.md#vpc-endpoint) provided by [AWS PrivateLink](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Introduction.html#what-is-privatelink)\. You can use the following [global condition keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#AvailableKeys) in IAM policies or [resource\-based policies](auth-and-access_resource-based-policies.md) to allow or deny access to requests from a particular VPC or VPC endpoint\.
+ `aws:SourceVpc` limits access to requests from the specified VPC\.
+ `aws:SourceVpce` limits access to requests from the specified VPC endpoint\.

If you use these condition keys in a secret policy statement that allows or denies access to Secrets Manager secrets, you can inadvertently deny access to services that use Secrets Manager to access secrets on your behalf\. Only some AWS services can run with an endpoint within your VPC\. If you restrict requests for a secret to a VPC or VPC endpoint, then calls to Secrets Manager from a service not configured for the service can fail\.