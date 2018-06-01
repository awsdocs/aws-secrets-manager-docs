# Actions, Resources, and Context Keys You Can Use in an IAM Policy for AWS Secrets Manager<a name="reference_iam-permissions"></a>

## Actions That You Can Reference in an IAM Policy<a name="iam-actions"></a>

The following table shows the permissions that you can specify in an IAM permissions policy to control access to your secrets\. Each permission on an "Action" can be associated with a "Resource" that specifies what the action can work on\. 

You can restrict use of some actions to only those secrets with Amazon Resource Names \(ARNs\) that match the `Resource` element in the policy\. See the section [Resources That You Can Reference in an IAM Policy ](#iam-resources) later in this topic\.

If you see an expand arrow \(**↗**\) in the upper\-right corner of the table, you can open the table in a new window\. To close the window, choose the close button \(**X**\) in the lower\-right corner\.


****  

|  Permission for "Action" element  |  API operation that's enabled by this action  |  Resource ARNs that can be used as a "Resource" with this action  |  Context keys that can be used with this action  | 
| --- | --- | --- | --- | 
|  secretsmanager:CancelRotateSecret  |  [CancelRotateSecret](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CancelRotateSecret.html)  |  [Secret](#iam-resources-secret)  |  [SecretId](#iam-contextkey-secretid)  | 
|  secretsmanager:CreateSecret  |  [CreateSecret](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CreateSecret.html)  |  |  [Name](#iam-contextkey-name) [Description](#iam-contextkey-description) [KmsKeyId](#iam-contextkey-kmskeyid)  | 
|  secretsmanager:DeleteSecret  |  [DeleteSecret](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DeleteSecret.html)  |  [Secret](#iam-resources-secret)  |  [SecretId](#iam-contextkey-secretid) [VersionId](#iam-contextkey-versionid)  | 
|  secretsmanager:DescribeSecret  |  [DesecribeSecret](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DescribeSecret.html)  |  [Secret](#iam-resources-secret)  |  [SecretId](#iam-contextkey-secretid)  | 
|  secretsmanager:GetRandomPassword  |  [GetRandomPassword](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetRandomPassword.html)  |  |  | 
|  secretsmanager:GetSecretValue  |  [GetSecretValue](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html)  |  [Secret](#iam-resources-secret)  |  [SecretId](#iam-contextkey-secretid) [VersionId](#iam-contextkey-versionid) [VersionStage](#iam-contextkey-versionstage)  | 
|  secretsmanager:ListSecrets  |  [ListSecrets](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ListSecrets.html)  |  |  | 
|  secretsmanager:ListSecretVersionIds  |  [ListSecretVersionIds](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ListSecretVersionIds.html)  |  [Secret](#iam-resources-secret)  |  [SecretId](#iam-contextkey-secretid)  | 
|  secretsmanager:PutSecretValue  |  [PutSecretValue](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutSecretValue.html)  |  [Secret](#iam-resources-secret)  |  [SecretId](#iam-contextkey-secretid)  | 
|  secretsmanager:RestoreSecret  |  [RestoreSecret](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RestoreSecret.html)  |  [Secret](#iam-resources-secret)  |  [SecretId](#iam-contextkey-secretid)  | 
|  secretsmanager:RotateSecret  |  [RotateSecret](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RotateSecret.html)  |  [Secret](#iam-resources-secret)  |  [SecretId](#iam-contextkey-secretid) [RotationLambdaArn](#iam-contextkey-rotationlambdaarn)  | 
|  secretsmanager:TagResource  |  [TagResource](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_TagResource.html)  |  [Secret](#iam-resources-secret)  |  [SecretId](#iam-contextkey-secretid)  | 
|  secretsmanager:UntagResource  |  [UntagResource](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UntagResource.html)  |  [Secret](#iam-resources-secret)  |  [SecretId](#iam-contextkey-secretid)  | 
|  secretsmanager:UpdateSecret  |  [UpdateSecret](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html)  |  [Secret](#iam-resources-secret)  |  [SecretId](#iam-contextkey-secretid) [Description](#iam-contextkey-description) [KmsKeyId](#iam-contextkey-kmskeyid)  | 
|  secretsmanager:UpdateSecretVersionStage  |  [UpdateSecretVersionStage](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecretVersionStage.html)  |  [Secret](#iam-resources-secret)  |  [SecretId](#iam-contextkey-secretid) [VersionStage](#iam-contextkey-versionstage)  | 

## Resources That You Can Reference in an IAM Policy<a name="iam-resources"></a>

The following table shows the ARN formats that are supported in IAM policies for AWS Secrets Manager\. You can view the IDs for each entity on the **Secret details** page for each secret in the Secrets Manager console\.

If you see an expand arrow \(**↗**\) in the upper\-right corner of the table, you can open the table in a new window\. To close the window, choose the close button \(**X**\) in the lower\-right corner\.<a name="iam-resources-secret"></a>


****  

| Resource Type | ARN Format | 
| --- | --- | 
|  Secret |  arn:aws:secretsmanager:*<Region>*:*<AccountId>*:secret:*OptionalPath/**SecretName*\-*6RandomCharacters*  | 

Secrets Manager constructs the last part of the ARN by appending a dash and six random alphanumeric characters at the end of your secret's name\. This helps ensure that if you ever delete a secret and then recreate another with the same name that individuals with permissions to the original secret don't automatically get access to the new secret because the six random characters will be different\.

## Context Keys That You Can Reference in an IAM Policy<a name="iam-contextkeys"></a>

Context keys in AWS Secrets Manager generally correspond to the request parameters of an API call\. This enables you to allow or block almost any request based on the value of a parameter\.

Each context key can be compared using a condition operator to a value that you specify\. The context keys that can be used depend on the action selected\. See the "Context keys" column in the [Actions](#iam-actions) section at the beginning of this topic\.

For example, you could choose to allow someone to retrieve *only* the `AWSCURRENT` version a secret value by using a `Condition` element similar to the following:

```
    "Effect": "Allow",
    "Condition": {"StringEqualsIgnoreCase" : {"VersionStage" : "AWSCURRENT"}}
```

The following table shows the context keys that you can specify in the `Condition` element of an IAM permissions policy to more granularly control access to an action\.


****  

|  Context keys for "Condition" element  | Description | 
| --- | --- | 
|   SecretId  |  Filters the request based on the unique identifier for the secret that's provided in the SecretId parameter\. The value can be either the friendly name or the ARN of the secret\. This enables you to limit which secrets can be accessed by a request\.  | 
|   Description  |  Filters the request based on the Description parameter in the request\.  | 
|   KmsKeyId  |  Filters the request based on the KmsKeyId parameter of the request\. This enables you to limit which keys can be used in a request\.  | 
|   Name  |  Filters the request based on Name parameter value of the request\. This enables you to restrict a secret's name to only those matching this value\.  | 
|   RotationLambdaArn  |  Filters the request based on the RotationLambdaARN parameter\. This enables you to restrict which Lambda rotation functions can be used with a secret\. It can be used with both CreateSecret and the operations that modify existing secrets\.  | 
|   VersionId  |  Filters the request based on the VersionId parameter of the request\. This enables you to restrict which versions of a secret can be accessed\.   | 
|   VersionStage  |  Filters the request based on the staging labels that are identified in the VersionStage parameter of a request\. This enables you to restrict access to only the secret versions that have a staging label that matches one of the values in this string array parameter\. Because this is a multi\-valued string array, [you must use one of the set operators to compare strings with this value\.](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_multi-value-conditions.html)  | 
|   resource/AllowRotationLambda  | Filters the request based on the ARN of the Lambda rotation function attached to the resource that the request is targeting\. This enables you to restrict access to only those secrets that already have a rotation Lambda ARN that matches this value\. | 
| resourcetag/tagname | Filters the request based on a tag attached to the secret\. Replace tagname with the actual tag name\. You can then use condition operators to ensure that the tag is present, and that it has the requested value\. | 