# Limiting access to specific secrets<a name="permissions_grant-limited-resources"></a>

In addition to restricting access to specific actions, you also can restrict access to specific secrets in your account\. The `Resource` elements in the previous examples all specify the wildcard character \("\*"\), which means "any resource this action can interact"\. Instead, you can replace the "\*" with the ARN of specific secrets you want to allow access\. 

**Example: Granting permissions to a single secret by name**  
The first statement of the following policy grants the user read access to the metadata about all of the secrets in the account\. However, the second statement allows the user to perform any Secrets Manager actions on only a single, specified secret by name—or on any secret beginning with the string "`another_secret_name-`" followed by exactly 6 characters:  
Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.  
Choose **Create Policy** and add the following code:  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "secretsmanager:DescribeSecret",
                "secretsmanager:List*"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "secretsmanager:*",
            "Resource": [
                "arn:aws:secretsmanager:region:1234-5678-9012:secret:a_specific_secret_name-a1b2c3",
                "arn:aws:secretsmanager:region:1234-5678-9012:secret:another_secret_name-??????"
            ]
        }
    ]
}
```
Using '??????' as a wildcard to match the 6 random characters assigned by Secrets Manager avoids a problem that occurs if you use the '\*' wildcard instead\. If you use the syntax "`another_secret_name-*`", Secrets Manager matches not just the intended secret with the 6 random characters, but also matches "`another_secret_name-<anything-here>a1b2c3`"\. Using the '??????' syntax enables you to securely grant permissions to a secret that doesn't yet exist\. This happens because you can predict all of the parts of the ARN except those 6 random characters\. Be aware, however, if you delete the secret and recreate it with the same name, the user automatically receives permission to the new secret, even though the 6 characters changed\.  
You get the ARN for the secret from the Secrets Manager console \(on the **Details** page for a secret\), or by calling the `List*` APIs\. The user or group you apply this policy to can perform any action \(`"secretsmanager:*"`\) on only the two secrets identified by the ARN in the example\.   
If you don't care about the region or account that owns a secret, you must specify a wildcard character \* , not an empty field, for the region and account ID number fields of the ARN\.

For more information about the ARNs for various resources, see [Resources you can reference in an IAM policy or secret policy](reference_iam-permissions.md#iam-resources)\. 