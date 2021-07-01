# Limiting access to specific actions<a name="permissions_grant-limited-actions"></a>

If you want to grant limited permissions instead of full permissions, you can create a policy that lists individual permissions you want to allow in the `Action` element of the IAM permissions policy\. As shown in the following example, you can use wildcard \(\*\) characters to grant only the `Describe*`, `Get*`, and `List*` permissions, which essentially provides read\-only access to your secrets\.

Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

Choose **Create Policy** and add the following code:

```
{
    "Version": "2012-10-17",
    "Statement": {
        "Effect": "Allow",
        "Action": [
            "secretsmanager:Describe*",
            "secretsmanager:Get*",
            "secretsmanager:List*" 
        ],
        "Resource": "*"
    }
}
```

For a list of all the permissions available to assign in an IAM policy, see [Actions, resources, and context keys you can use in an IAM policy or secret policy for AWS Secrets Manager](reference_iam-permissions.md)\.