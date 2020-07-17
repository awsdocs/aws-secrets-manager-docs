# Using Identity\-based Policies \(IAM Policies\) for Secrets Manager<a name="auth-and-access_identity-based-policies"></a>

As the administrator of an account, you can control access to the AWS resources in your account by attaching permissions policies to IAM identities such as users, groups, and roles\. When granting permissions, you decide the permissions, the resources, and the specific actions to allow on those resources\. If you grant permissions to a role, users in other accounts you specify can assume that role\.

By default, a user or role has no permissions of any kind\. You must explicitly grant any permissions by using a policy\. If you don't explicitly grant permission, then the permission implicitly denies\. If you explicitly deny a permission, then that overrules any other policy allowing it\. In other words, a user has only permissions explicitly granted and not explicitly denied\.

For an overview of the basic elements for policies, see [Managing Access to Resources with Policies](auth-and-access_overview.md#auth-and-access_resource-access)\.

**Topics**
+ [AWS Managed Policy for Secrets Manager](#available-managed-policies)
+ [Granting Full Secrets Manager Administrator Permissions to a User](#permissions_grant-admin-actions)
+ [Granting Read Access to One Secret](#permissions_grant-get-secret-value-to-one-secret)
+ [Limiting Access to Specific Actions](#permissions_grant-limited-actions)
+ [Limiting Access to Specific Secrets](#permissions_grant-limited-resources)
+ [Limiting Access to Secrets with Specific Staging Labels or Tags](#permissions_grant-limited-condition)
+ [Granting a Rotation Function Permission to Access a Separate Master Secret](#permissions-grant-rotation-role-access-to-master-secret)

## AWS Managed Policy for Secrets Manager<a name="available-managed-policies"></a>

AWS Secrets Manager provides the following AWS managed policy to allow granting permissions easier\. Choose the link to view the policy in the IAM console\.
+ [SecretsManagerReadWrite](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/SecretsManagerReadWrite) – This policy grants the access required to administer Secrets Manager, except the policy doesn't include the IAM permissions required to create roles and attach policies to those roles\. For those permissions, attach the [IAMFullAccess](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/IAMFullAccess) managed policy\. For instructions, see the following section [Granting Full Secrets Manager Administrator Permissions to a User](#permissions_grant-admin-actions)\.

## Granting Full Secrets Manager Administrator Permissions to a User<a name="permissions_grant-admin-actions"></a>

To be a Secrets Manager administrator, you must have permissions in several services\. We recommended you don't enable Secrets Manager as an end\-user service enabling your users to create and manage their secrets\. The permissions required to enable rotation grant significant permissions standard users shouldn't have\. Instead, trusted administrators manage the Secrets Manager service\. The end user no longer handles the credentials directly or embed them in code\.

**Warning**  
When you enable rotation, Secrets Manager creates a Lambda function and attaches an IAM role to the function\. This requires several IAM permissions granted only to trusted individuals\. Therefore, the managed policy for Secrets Manager *does not* include these IAM permissions\. Instead, you must explicitly choose to assign the `IAMFullAccess` managed policy, in addition to the `SecretsManagerReadWrite` managed policy to create a full Secrets Manager administrator\.  
Granting access with only the `SecretsManagerReadWrite` policy enables an IAM user to create and manage secrets, but the user can't create and configure the Lambda rotation functions required to enable rotation\.

Complete the following steps to grant full Secrets Manager administrator permissions to an IAM user, group, or role in your account\. In this example, you don't create a new policy\. Instead, you attach an AWS managed policy preconfigured with the permissions\.

**To grant full administrator permissions to an IAM user, group, or role**

1. Sign in to the Identity and Access Management \(IAM\) console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/) as a user with permissions to attach IAM policies to other IAM users\.

   In the IAM console, navigate to **Policies**\.

1. For **Filter Policies: Policy type**, choose **AWS managed**, and then in the **Search** box, start typing **SecretsManagerReadWrite** until you can see the policy in the list\.

1. Choose the **SecretsManagerReadWrite** policy name\.

1. Choose the **Policy actions** , and then choose **Attach**\.

1. Check the box next to the users, groups, or roles you want to add as a Secrets Manager administrator\.

1. Choose **Attach policy**\.

1. Repeat steps 1 through 6 to also attach the **IAMFullAccess** policy\.

The selected users, groups, and roles can immediately begin performing tasks in Secrets Manager\.

## Granting Read Access to One Secret<a name="permissions_grant-get-secret-value-to-one-secret"></a>

When you write an application to use Secrets Manager to retrieve and use a secret, you only need to grant the application a very limited set of permissions\. Secrets Manager only requires the permission for the action that allows retrieval of the encrypted secret value with the credentials\. You specify the Amazon Resource Name \(ARN\) of the secret to restrict access to only the one secret\.

Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

Choose **Create Policy** and add the following code:

```
{
    "Version": "2012-10-17",
    "Statement": {
        "Effect": "Allow",
        "Action": "secretsmanager:GetSecretValue",
        "Resource": "<arn-of-the-secret-the-app-needs-to-access>"
    }
}
```

For a list of all the permissions available to assign in an IAM policy, see [Actions, Resources, and Context Keys You Can Use in an IAM Policy or Secret Policy for AWS Secrets Manager](reference_iam-permissions.md)\.

## Limiting Access to Specific Actions<a name="permissions_grant-limited-actions"></a>

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

For a list of all the permissions available to assign in an IAM policy, see [Actions, Resources, and Context Keys You Can Use in an IAM Policy or Secret Policy for AWS Secrets Manager](reference_iam-permissions.md)\.

## Limiting Access to Specific Secrets<a name="permissions_grant-limited-resources"></a>

In addition to restricting access to specific actions, you also can restrict access to specific secrets in your account\. The `Resource` elements in the previous examples all specify the wildcard character \("\*"\), which means "any resource this action can interact"\. Instead, you can replace the "\*" with the ARN of specific secrets you want to allow access\. 

**Example Example: Granting permissions to a single secret by name**  
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
                "arn:aws:secretsmanager:<region>:<account-id-number>:secret:a_specific_secret_name-a1b2c3",
                "arn:aws:secretsmanager:<region>:<account-id-number>:secret:another_secret_name-??????"
            ]
        }
    ]
}
```
Using '??????' as a wildcard to match the 6 random characters assigned by Secrets Manager avoids a problem that occurs if you use the '\*' wildcard instead\. If you use the syntax "`another_secret_name-*`", Secrets Manager matches not just the intended secret with the 6 random characters, but also matches "`another_secret_name-<anything-here>a1b2c3`"\. Using the '??????' syntax enables you to securely grant permissions to a secret that doesn't yet exist\. This happens because you can predict all of the parts of the ARN except those 6 random characters\. Be aware, however, if you delete the secret and recreate it with the same name, the user automatically receives permission to the new secret, even though the 6 characters changed\.  
You get the ARN for the secret from the Secrets Manager console \(on the **Details** page for a secret\), or by calling the `List*` APIs\. The user or group you apply this policy to can perform any action \(`"secretsmanager:*"`\) on only the two secrets identified by the ARN in the example\.   
If you don't care about the region or account that owns a secret, you must specify a wildcard character \* , not an empty field, for the region and account ID number fields of the ARN\.

For more information about the ARNs for various resources, see [Resources You Can Reference in an IAM Policy or Secret Policy](reference_iam-permissions.md#iam-resources)\. 

## Limiting Access to Secrets with Specific Staging Labels or Tags<a name="permissions_grant-limited-condition"></a>

In any of the previous examples, you called out actions, resources, and principals explicitly by name or ARN only\. You can also refine access to include only those secrets with metadata contains a certain tag key and value, or a secret with a specific label\.

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

## Granting a Rotation Function Permission to Access a Separate Master Secret<a name="permissions-grant-rotation-role-access-to-master-secret"></a>

When you create a Lambda rotation function using the provided AWS Serverless Application Repository template, either by using the console or by using AWS CLI commands, a default policy attached to the function role controls the function actions\. By default, this policy grants access *only* to secrets with this Lambda function configured as the secret rotation function\.

If the credentials in the secret don't allow users permission to change their password on the secured database or service, then you need to use a separate set of credentials with elevated permissions \(a *superuser*\) to change this secret credentials during rotation\. Secrets Manager stores the superuser credentials in a separate "master" secret\. Then, when you rotate your secret, the Lambda rotation function signs in to the database or service with the master credentials to change or update the secret credentials\. If you choose to implement this strategy, then you must add an additional statement to the role policy attached to the function that grants access to this master secret, in addition to the main secret\.

If you use the console to configure a rotation with a strategy using a master secret, then you can select the master secret when you configure rotation for the secret\. 

**Important**  
You must have the **GetSecretValue** permission for the master secret to select it in the console\.

After you configure rotation in the console, you must manually perform the following steps to grant the Lambda function access to the master secret\.

**To grant a Lambda rotation function access to a master secret**  
Follow the steps in one of the following tabs:

------
#### [ Using the console ]

1. When you finish creating a secret with rotation enabled or editing your secret to enable rotation, the console displays a message similar to the following:

   ```
   Your secret MyNewSecret has been successfully stored [and secret rotation is enabled].
   To finish configuring rotation, you need to grant the role MyLambdaFunctionRole permission to retrieve the secret <ARN of master secret>.
   ```

1. Copy the ARN of the master secret in the message to your clipboard\. You use it in a later step\.

1. The role name in the preceding message provides a link to the IAM console, and navigates directly to the role for you\. Choose that link\.

1. On the **Permissions** tab, there can be one or two inline policies\. Secrets Manager names one as `SecretsManager<Name of Template>0`\. The template contains the EC2\-related permissions required when both the rotation function and you run your secured service in a VPC, and not directly accessible from the internet\. The other template named `SecretsManager<Name of Template>1`\.contains the permissions to enable the rotation function to call Secrets Manager operations\. Open the policy \(the one ending with "1"\) by choosing the expand arrow to the left of the policy and examining the permissions\.

1. Choose **Edit policy**, and then perform the steps in one of the following tabs:

------
#### [ Using the IAM Visual Editor ]

   On the **Visual editor** tab, choose **Add additional permissions**, and then set the following values:
   + For **Service**, choose **Secrets Manager**\.
   + For **Actions**, choose **GetSecretValue**\.
   + For **Resources**, choose **Add ARN** next to the **secret** resource type entry\.
   + In the **Add ARN\(s\)** dialog box, paste the ARN of the master secret you copied previously\.

------
#### [ Using the JSON editor ]

   On the **JSON** tab, examine the top of the script\. Between lines 3 \(`"Statement": [`\) and line 4 \( `{` \), enter the following lines:

   ```
           {
               "Action": "secretsmanager:GetSecretValue",
               "Resource": "arn:aws:secretsmanager:region:123456789012:secret:MyDatabaseMasterSecret",
               "Effect": "Allow"
           },
   ```

------

1. After you finish editing the policy, choose **Review policy**, and then **Save changes**\.

1. You can now close the IAM console and return to the Secrets Manager console\.

------

**Note**  
For a complete list of actions, resources, and context keys you can use with Secrets Manager, see [Actions, Resources, and Condition Keys for AWS Secrets Manager](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awssecretsmanager.html)\.