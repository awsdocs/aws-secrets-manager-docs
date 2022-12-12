# Attach a permissions policy to an AWS Secrets Manager secret<a name="auth-and-access_resource-policies"></a>

In a resource\-based policy, you specify who can access the secret and the actions they can perform on the secret\. You can use resource\-based policies to:
+ Grant access to a single secret to multiple users and roles\. 
+ Grant access to users or roles in other AWS accounts\.

See [Permissions policy examples for AWS Secrets Manager](auth-and-access_examples.md)\.

When you attach a resource\-based policy to a secret in the console, Secrets Manager uses the automated reasoning engine [Zelkova](https://aws.amazon.com/blogs/security/protect-sensitive-data-in-the-cloud-with-automated-reasoning-zelkova/) and the API `ValidateResourcePolicy` to prevent you from granting a wide range of IAM principals access to your secrets\. Alternatively, you can call the `PutResourcePolicy` API with the `BlockPublicPolicy` parameter from the CLI or SDK\. 

**To view, change, or delete the resource policy for a secret \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the secret details page for your secret, in the **Resource permissions** section, choose **Edit permissions**\.

1. In the code field, do one of the following, and then choose **Save**:
   + To attach or modify a resource policy, enter the policy\. 
   + To delete the policy, clear the code field\.

## AWS CLI<a name="auth-and-access_resource_cli"></a>

**Example Retrieve a resource policy**  
The following [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-resource-policy.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-resource-policy.html) example retrieves the resource\-based policy attached to a secret\.  

```
aws secretsmanager get-resource-policy \
    --secret-id MyTestSecret
```

**Example Delete a resource policy**  
The following [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/delete-resource-policy.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/delete-resource-policy.html) example deletes the resource\-based policy attached to a secret\.  

```
aws secretsmanager delete-resource-policy \
    --secret-id MyTestSecret
```

**Example Add a resource policy**  
The following [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/put-resource-policy.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/put-resource-policy.html) example adds a permissions policy to a secret, checking first that the policy does not provide broad access to the secret\. The policy is read from a file\. For more information, see [Loading AWS CLI parameters from a file](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-parameters-file.html) in the AWS CLI User Guide\.  

```
aws secretsmanager put-resource-policy \
    --secret-id MyTestSecret \
    --resource-policy file://mypolicy.json \
    --block-public-policy
```
Contents of `mypolicy.json`:  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::123456789012:role/MyRole"
            },
            "Action": "secretsmanager:GetSecretValue",
            "Resource": "*"
        }
    ]
}
```

## AWS SDK<a name="auth-and-access_resource_sdk"></a>

To retrieve the policy attached to a secret, use [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetResourcePolicy.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetResourcePolicy.html) \.

To delete a policy attached to a secret, use [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DeleteResourcePolicy.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DeleteResourcePolicy.html)\.

To attach a policy to a secret, use [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutResourcePolicy.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutResourcePolicy.html)\. If there is already a policy attached, the command first removes it, and then attaches the new policy\. The policy must be formatted as JSON structured text\. See [JSON policy document structure](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html#policies-introduction)\. Use the [Permissions policy examples for AWS Secrets Manager](auth-and-access_examples.md) to get started writing your policy\.

For more information, see [AWS SDKs](asm_access.md#asm-sdks)\.