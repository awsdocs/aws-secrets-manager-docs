# Granting read access to one secret<a name="permissions_grant-get-secret-value-to-one-secret"></a>

When you write an application to use Secrets Manager to retrieve and use a secret, you only need to grant the application a very limited set of permissions\. Secrets Manager only requires the permission for the action that allows retrieval of the encrypted secret value with the credentials\. You specify the Amazon Resource Name \(ARN\) of the secret to restrict access to only the one secret\.

Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

Choose **Create Policy** and add the following code\. You can find the ARN for your secret in the Secrets Manager console on the **Secret details** page under **Secret ARN**\.

```
{
    "Version": "2012-10-17",
    "Statement": {
        "Effect": "Allow",
        "Action": "secretsmanager:GetSecretValue",
        "Resource": "SecretARN"
    }
}
```

For a list of all the permissions available to assign in an IAM policy, see [Actions, resources, and context keys you can use in an IAM policy or secret policy for AWS Secrets Manager](reference_iam-permissions.md)\.