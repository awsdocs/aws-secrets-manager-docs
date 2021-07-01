# Resource\-based policies<a name="auth-and-access_resource-policies"></a>

Each Secrets Manager secret can have one resource\-based permissions policy,a *secret policy*, attached to it\. You can use a secret policy to grant cross\-account permissions as an alternative to using identity\-based policies with IAM roles\. For example, you can grant permissions to a user in Account B to access your secret in Account A by adding permissions to the secret policy, and identifying the user in Account B as a `Principal`, instead of creating an IAM role\.

The following example describes a Secrets Manager secret policy with one statement\. The statement allows the administrator of account 123456789012 to delegate permissions to users and roles in that account\. The policy limits the permissions the administrator can delegate to the `secretsmanager:GetSecretValue` action on a single secret named "prod/ServerA\-a1b2c3"\. The condition ensures the user can retrieve only the current version of the secret\.

```
{
    "Version" : "2012-10-17",
    "Statement" : [
        {
            "Effect": "Allow",
            "Principal": {"AWS": "arn:aws:iam::123456789012:root" },
            "Action": "secretsmanager:GetSecretValue",
            "Resource": "arn:aws:secretsmanager:<region>:<account_id>:secret:prod/ServerA-a1b2c3",
            "Condition": {
                "ForAnyValue:StringEquals": {
                    "secretsmanager:VersionStage" : "AWSCURRENT"
                }
            }
        }
    ]
}
```

**Important**  
When an IAM principal from one account accesses a secret in a different account, the secret must be encrypted by using a custom AWS KMS CMK\. A secret encrypted with the default Secrets Manager CMK for the account can be decrypted only by principals in that account\. A principal from a different account must be granted permission to both the secret and to the custom AWS KMS CMK\.   
As an alternative, you can grant the user cross\-account access to assume an IAM role in the same account as the secret\. Because the role exists in the same account as the secret, the role can access secrets encrypted with the default AWS KMS key for the account\.

Because a secret version can have multiple staging labels attached, you need to use the [IAM policy language's set operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_multi-value-conditions.html) to compare them in a policy\. In the previous example, `ForAnyValue:StringLike` states that if any one of the labels attached to the version under evaluation matches "`AWSCURRENT`", then the statement matches, and applies the `Effect`\.

For more example resource\-based policies with Secrets Manager, see [Using resource\-based policies for Secrets Manager](auth-and-access_resource-based-policies.md)\. For additional information about using resource\-based policies instead of IAM roles \(identity\-based policies\), see [How IAM Roles Differ from Resource\-based Policies](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_compare-resource-policies.html) in the *IAM User Guide*\.