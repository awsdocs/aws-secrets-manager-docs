# Identity\-based policies<a name="auth-and-access_iam-policies"></a>

You can attach policies to IAM identities\. For example, you can do the following:
+ **Attach a permissions policy to a user or a group in your account** – To grant a user permissions to create a secret, you can attach a permissions policy directly to the user or to a group the user belongs to \(recommended\)\.
+ **Attach a permissions policy to a role** – You can attach a permissions policy to an IAM role to grant access to a secret to anyone assuming the role\. This includes users in the following scenarios:
  + **Federated users** – You can grant permissions to users authenticated by an identity system other than IAM\. For example, you can associate IAM roles to mobile app users signing in using Amazon Cognito\. The role grants the app temporary credentials with the permissions in the role permission policy\. Those permissions can include access to a secret\. For more information, see [What is Amazon Cognito?](https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html) in the *Amazon Cognito Developer Guide*\. 
  + **Applications running on EC2 instances** – You can grant permissions to applications running on an Amazon EC2 instance by attaching an IAM role to the instance\. When an application in the instance invokes an AWS API, the application can get AWS temporary credentials from the instance metadata\. These temporary credentials associate with a role, and limited by the role permissions policy\. Those permissions can include access to a secret\.
  + **Cross\-account access** – An administrator in Account A can create a role to grant permissions to a user in a different Account B\. For example:

    1. The Account A administrator creates an IAM role and attaches a permissions policy to the role\. The policy grants access to secrets in account A and specifies the actions users perform on the secrets\.

    1. The Account A administrator attaches a trust policy to the role that identifies the account ID of Account B in the `Principal` element to specify who assumes the role\.

    1. The Account B administrator can then delegate permissions to any users in account B to enable them to assume account A role\. Doing this allows users in account B to access the secrets in the first account\. 

For more information about using IAM to delegate permissions, see [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) in the *IAM User Guide*\.

The following example policy can be attached to a user, group, or role\. The policy allows the affected user or role to perform the `DescribeSecret` and `GetSecretValue` operations in your account only on secrets with a name beginning with the path "TestEnv/"\. The policy also restricts the user or role to retrieving only the version of the secret with the staging label `AWSCURRENT` attached\. Replace the *<region>* and *<account\_id>* placeholders in the following examples with your actual values\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
           "Sid" : "Stmt1DescribeSecret",  
           "Effect": "Allow",
           "Action": [ "secretsmanager:DescribeSecret" ],
           "Resource": "arn:aws:secretsmanager:region:account_idsecret:TestEnv/*"
        },
        {
            "Sid" : "Stmt2GetSecretValue",  
            "Effect": "Allow",
            "Action": [ "secretsmanager:GetSecretValue" ],
            "Resource": "arn:aws:secretsmanager:region:account_id:secret:TestEnv/*",
            "Condition" : { 
                "ForAnyValue:StringLike" : {
                    "secretsmanager:VersionStage" : "AWSCURRENT" 
                } 
            }
        }
    ]
}
```

Because a secret version can have multiple staging labels attached, you need to use the [IAM policy language's "set operators"](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_multi-value-conditions.html) to compare them in a policy\. In the previous example, `ForAnyValue:StringLike` states if any one of the staging labels attached to the secret version under evaluation matches the string "`AWSCURRENT`", then the statement matches, and applies the string `Effect`\.

For more example identity\-based policies, see [Using identity\-based policies \(IAM policies\) and ABAC for Secrets Manager](auth-and-access_identity-based-policies.md)\. For more information about users, groups, roles, and permissions, see [Identities \(Users, Groups, and Roles\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html) in the *IAM User Guide*\.