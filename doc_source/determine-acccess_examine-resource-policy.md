# Examining the resource policy<a name="determine-acccess_examine-resource-policy"></a>

You can view the secret policy document attached to a secret by using the `[GetResourcePolicy](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetResourcePolicy.html)` operation, and examining the `ResourcePolicy` response element\.

Examine the resource policy document and take note of all principals specified in each policy statement `Principal` element\. IAM users, IAM roles, and AWS accounts in the `Principal`elements all have access to this secret\.

**Example Policy Statement 1**

```
{
    "Sid": "Allow users or roles in account 123456789012 who are delegated access by that account's administrator to have read access to the secret",
    "Effect": "Allow",
    "Principal":  {"AWS": "arn:aws:iam::123456789012:root"},
    "Action": [
        "secretsmanager:List*",
        "secretsmanager:Describe*",
        "secretsmanager:Get*"
    ],
    "Resource": "*"
}
```

In the preceding policy statement, `&region-arn;iam::123456789012:root` refers to the AWS account 123456789012 and allows the administrator of that account to grant access to any users or roles in the account\. 

You must also [examine all IAM policies](https://docs.aws.amazon.com/kms/latest/developerguide/determining-access.html#determining-access-iam-policies) in all AWS accounts listed as principals to determine if the policies allow access to this secret\.

**Example Policy Statement 2**

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

In the preceding policy statement, `&region-arn;iam::111122223333:user/anaya` refers to the IAM user named Anaya in AWS account 111122223333\. This user can perform all Secrets Manager actions\.

**Example Policy Statement 3**

```
{
    "Sid": "Allow an app associated with an IAM role to only read the current version of a secret",
    "Effect": "Allow",
    "Principal":  {"AWS": "arn:aws:iam::123456789012:role/EncryptionApp" },
    "Action": ["secretsmanager:GetSecretValue"],
    "Condition": { "ForAnyValue:StringEquals": {"secretsmanager:VersionStage": "AWSCURRENT" } },
    "Resource": "*"
}
```

In the preceding policy statement, `&region-arn;iam::123456789012:role/EncryptionApp` refers to the IAM role named EncryptionApp in AWS account 123456789012\. Principals assuming this role can perform the one action listed in the policy statement\. This action obtain the details for the current version of the secret\.

To learn all the different ways you can specify a principal in a secret policy document, see [Specifying a Principal](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Principal_specifying) in the *IAM User Guide*\.

To learn more about Secrets Manager secret policies, see [Resource\-based policies](auth-and-access_resource-policies.md)\.