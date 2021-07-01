# Controlling access to secrets for principals<a name="permissions_grant-limited-principal"></a>

When you use a resource\-based policy with Secrets Manager attached directly to a secret, the `Resource` automatically and implicitly becomes the secret with the attached policy\. You can now specify the `Principal` element\. The `Principal` element enables you to specify the IAM users, groups, roles, accounts, or AWS service principals that can access this secret, and the actions they can perform on this secret\. For more information about all the different ways to specify a `Principal`, see [Principal](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Principal) in the *IAM User Guide*\.

**Example: Granting permission to a user in the same account as the secret**  
The following policy, when attached directly to a secret as part of the metadata, grants the user Anaya permission to run any Secrets Manager operation on the secret\.\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "secretsmanager:*",
            "Principal": {"AWS": "arn:aws:iam::123456789012:user/anaya"},
            "Resource": "*"
        }
    ]
}
```

**Example: Granting permission to authorized users in a different account**  
The following policy, when attached directly to a secret as part of the metadata, grants the IAM user Mateo in the account 123456789012 access to read any version of a specific secret:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {"AWS": "arn:aws:iam::123456789012:user/mateo" },
            "Action": "secretsmanager:GetSecretValue",
            "Resource": "*"
        }
    ]
}
```