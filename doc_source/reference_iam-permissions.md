# Permissions reference for Secrets Manager<a name="reference_iam-permissions"></a>

To see the elements that make up a permissions policy, see [JSON policy document structure](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html#policies-introduction) and [IAM JSON policy elements reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html)\. 

To get started writing your own permissions policy, see [Permissions policy examples](auth-and-access_examples.md)\.

## Secrets Manager actions<a name="reference_iam-permissions_actions"></a>


****  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html)

## Secrets Manager resources<a name="iam-resources"></a>


****  

| Resource types | ARN | Condition keys | 
| --- | --- | --- | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-resources](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-resources)  |  arn:$\{Partition\}:secretsmanager:$\{Region\}:$\{Account\}:secret:$\{SecretId\}  |   [aws:RequestTag/$\{TagKey\}](#awssecretsmanager-aws_RequestTag___TagKey_)   [aws:ResourceTag/$\{TagKey\}](#awssecretsmanager-aws_ResourceTag___TagKey_)   [aws:TagKeys](#awssecretsmanager-aws_TagKeys)   [secretsmanager:ResourceTag/tag\-key](#awssecretsmanager-secretsmanager_ResourceTag_tag-key)   [secretsmanager:resource/AllowRotationLambdaArn](#awssecretsmanager-secretsmanager_resource_AllowRotationLambdaArn)   | 

Secrets Manager constructs the last part of the secret ARN by appending a dash and six random alphanumeric characters at the end of the secret name\. If you delete a secret and then recreate another with the same name, this formatting helps ensure that individuals with permissions to the original secret don't automatically get access to the new secret because Secrets Manager generates six new random characters\.

You can find the ARN for a secret in the Secrets Manager console on the secret details page or by calling [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DescribeSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DescribeSecret.html)\.

## Condition keys<a name="iam-contextkeys"></a>

If you include string conditions from the following table in your permissions policy, callers to Secrets Manager must pass the matching parameter or they are denied access\. To avoid denying callers for a missing parameter, add `IfExists` to the end of the condition operator name, for example `StringLikeIfExists`\. For more information, see [IAM JSON policy elements: Condition operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html)\.


****  

| Condition keys | Description | Type | 
| --- | --- | --- | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by a key that is present in the request the user makes to the Secrets Manager service | String | 
|   [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-resourcetag](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-resourcetag)  | Filters access by the tags associated with the resource | String | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by the list of all the tag key names present in the request the user makes to the Secrets Manager service | String | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by the list of Regions in which to replicate the secret | ArrayOfString | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by whether the resource policy blocks broad AWS account access | Bool | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by the description text in the request | String | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by whether the secret is to be deleted immediately without any recovery window | Bool | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by whether to overwrite a secret with the same name in the destination Region | Bool | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by the ARN of the KMS key in the request | String | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by whether the rotation rules of the secret are to be modified | Bool | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by the friendly name of the secret in the request | String | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by the number of days that Secrets Manager waits before it can delete the secret | Numeric | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by a tag key and value pair | String | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by whether the secret is to be rotated immediately | Bool | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by the ARN of the rotation Lambda function in the request | ARN | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by the SecretID value in the request | ARN | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by primary region in which the secret is created | String | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by the unique identifier of the version of the secret in the request | String | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by the list of version stages in the request | String | 
|   [https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_iam-permissions.html#iam-contextkeys)  | Filters access by the ARN of the rotation Lambda function associated with the secret | ARN | 

## Block broad access to secrets with `BlockPublicPolicy` condition<a name="iam-contextkeys-blockpublicpolicy"></a>

In identity policies that allow the action `PutResourcePolicy`, we recommend you use `BlockPublicPolicy: true`\. This condition means that users can only attach a resource policy to a secret if the policy doesn't allow broad access\. The following example shows how to use `BlockPublicPolicy`\.

```
{
    "Version": "2012-10-17",
    "Statement": {
        "Effect": "Allow",
        "Action": "secretsmanager:PutResourcePolicy",
        "Resource": "SecretId",
        "Condition": {
            "Bool": {
                "secretsmanager:BlockPublicPolicy": "true"
            }
        }
    }
}
```

## IP address conditions<a name="iam-contextkeys-ipaddress"></a>

Use caution when you specify the [IP address condition operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html#Conditions_IPAddress) or the `aws:SourceIp` condition key in a policy statement that allows or denies access to Secrets Manager\. For example, if you attach a policy that restricts AWS actions to requests from your corporate network IP address range to a secret, then your requests as an IAM user invoking the request from the corporate network work as expected\. However, if you enable other services to access the secret on your behalf, such as when you enable rotation with a Lambda function, that function calls the Secrets Manager operations from an AWS\-internal address space\. Requests impacted by the policy with the IP address filter fail\.

Also, the `aws:sourceIP` condition key is less effective when the request comes from an Amazon VPC endpoint\. To restrict requests to a specific VPC endpoint, use [VPC endpoint conditions](#iam-contextkeys-vpcendpoint)\.

## VPC endpoint conditions<a name="iam-contextkeys-vpcendpoint"></a>

To allow or deny access to requests from a particular VPC or VPC endpoint, use `aws:SourceVpc` to limit access to requests from the specified VPC or `aws:SourceVpce` to limit access to requests from the specified VPC endpoint\. See [Example: Permissions and VPCs](auth-and-access_examples.md#auth-and-access_examples_vpc)\.
+ `aws:SourceVpc` limits access to requests from the specified VPC\.
+ `aws:SourceVpce` limits access to requests from the specified VPC endpoint\.

If you use these condition keys in a resource policy statement that allows or denies access to Secrets Manager secrets, you can inadvertently deny access to services that use Secrets Manager to access secrets on your behalf\. Only some AWS services can run with an endpoint within your VPC\. If you restrict requests for a secret to a VPC or VPC endpoint, then calls to Secrets Manager from a service not configured for the service can fail\.

See [Using AWS Secrets Manager with a VPC endpoint](vpc-endpoint-overview.md)\.