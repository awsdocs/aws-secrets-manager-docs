# Limiting access to secrets with specific staging labels or tags \(ABAC\)<a name="permissions_grant-limited-condition"></a>

In any of the previous examples, you called out actions, resources, and principals explicitly by name or ARN only\. You can also refine access to include only those secrets with metadata contains a certain tag key and value, or a secret with a specific label\.

You can attach tags to IAM principals \(users or roles\) and to AWS resources\. You can then define policies that use tag condition keys to grant permissions to your principals based on their tags\. When you use tags to control access to your AWS resources, you allow your teams and resources to grow with fewer changes to AWS policies\. ABAC policies are more flexible than traditional AWS policies, which require you to list each individual resource\.

For example, you can create three roles with the `access-project` tag key\. Set the tag value of the first role to `Heart`, the second to `Sun`, and the third to `Lightning`\. You can then use a single policy that allows access when you tag the role and the resource with the same value for `access-project`\. 

To implement ABAC for your secrets, follow this tutorial: [IAM Tutorial: Define permissions to access AWS resources based on tags\.](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_attribute-based-access-control.html)

**Example: Granting permission to a secret containing metadata with a certain tag key and value**  
The following policy, when attached to a user, group, or role, allows the user to use `DescribeSecret` on any secret in the current account with metadata containing a tag with the key "ServerName" and the value "ServerABC"\.

Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

Choose **Create Policy** and add the following code:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "secretsmanager:DescribeSecret",
            "Resource": "*",
            "Condition": { "StringEquals": { "secretsmanager:ResourceTag/ServerName": "ServerABC" } }
        }
    ]
}
```



**Example: Granting permission to the version of the secret with a certain staging label**  
The following policy, when attached to a user, group, or role, allows the user to use `GetSecretValue` on any secret with a name that begins with `Prod`—and only for the version with the staging label `AWSCURRENT` attached\.

Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

Choose **Create Policy** and add the following code:

```
{
    "Policy": {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "secretsmanager:GetSecret",
                "Resource": "arn:aws:secretsmanager:*:*:secret:Prod*",
                "Condition": { "ForAnyValue:StringEquals": { "secretsmanager:VersionStage": "AWSCURRENT" } }
            }
        ]
    }
}
```

Because a version of a secret can have multiple staging labels attached, you need to use the [IAM policy language's set operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_multi-value-conditions.html) to compare them\. In the previous example, `ForAnyValue:StringLike` states if any one of the staging labels attached to the version under evaluation matches the string `AWSCURRENT`, then the statement matches, and applies the `Effect`\.