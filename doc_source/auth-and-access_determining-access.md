# Determining Access to a Secret<a name="auth-and-access_determining-access"></a>

To determine the full extent of who or what currently has access to a secret in AWS Secrets Manager, you must examine both the secret's policy \(if one exists\), and all AWS Identity and Access Management \(IAM\) policies that are attached to either the IAM user and its groups, or the IAM role\. You might do this to determine the scope of potential usage of a secret, or to help you meet compliance or auditing requirements\. The following topics can help you generate a complete list of the AWS principals \(identities\) that currently have access to a secret\.

**Topics**
+ [Understanding Policy Evaluation](#determine-acccess_understanding-policy-evaluation)
+ [Examining the Secret Policy](#determine-acccess_examine-secret-policy)
+ [Examining IAM Policies](#determine-acccess_examine-iam-policies)

## Understanding Policy Evaluation<a name="determine-acccess_understanding-policy-evaluation"></a>

When authorizing access to a secret, Secrets Manager evaluates the secret policy that's attached to the secret, and all IAM policies that are attached to the IAM user or role making the request\. To do this, Secrets Manager uses a process that's similar to the one described in [Determining Whether a Request is Allowed or Denied](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html#policy-eval-denyallow) in the *IAM User Guide*\.

For example, assume that you have two secrets and three users, all in the same AWS account\. The secrets and users have the following policies:
+ Secret 1 doesn't have a secret policy\.
+ Secret 2 has a secret policy that allows access to the user principals Ana and Carlos\.
+ Ana has no IAM policy that references Secret 1 or Secret 2\.
+ Bob's IAM policy allows all Secrets Manager actions for all secrets\.
+ Carlos' IAM policy denies all Secrets Manager actions for all secrets\.

The results of this are as follows:
+ Ana can access only Secret 2\. She doesn't have a policy that's attached to her user, but Secret 2 explicitly grants her access\.
+ Bob can access both Secret 1 and Secret 2 because Bob has an IAM policy that allows access to all secrets in the account, and there's no explicit "deny" to override that\.
+ Carlos can't access Secret 1 or Secret 2 because all Secrets Manager actions are denied by his IAM policy\. The explicit deny in Carlos' IAM policy overrides the explicit "allow" that's in the secret policy attached to Secret 2\.

In summary, all policy statements from either the secret or the identity that "allow" access are added together\. Any explicit deny overrides any allow to the overlapping action and resource\.

## Examining the Secret Policy<a name="determine-acccess_examine-secret-policy"></a>

You can view the secret policy document that's attached to a secret by using the `[GetSecretPolicy](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretPolicy.html)` operation, and examining the `ResourcePolicy` response element\.

Examine the secret policy document and take note of all principals that are specified in each policy statement's `Principal` element\. IAM users, IAM roles, and AWS accounts in the `Principal`elements all have access to this secret\.

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

[Suggest improvements to this example on GitHub\.](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/iam_policies/secretsmanager/asm-resource-policy-grants-xacct-read.json)

In the preceding policy statement, `arn:aws:iam::111122223333:root` refers to the AWS account 111122223333 and allows the administrator of that account to grant access to any users or roles in that account\. 

You must also [examine all IAM policies](http://docs.aws.amazon.com/kms/latest/developerguide/determining-access.html#determining-access-iam-policies) in all AWS accounts that are listed as principals to determine whether they allow access to this secret\.

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

[Suggest improvements to this example on GitHub\.](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/iam_policies/secretsmanager/asm-resource-policy-grant-all-perms-to-anaya.json)

In the preceding policy statement, `arn:aws:iam::111122223333:user/anaya` refers to the IAM user named Anaya in AWS account 111122223333\. This user is allowed to perform all Secrets Manager actions\.

**Example Policy Statement 3**

```
{
    "Sid": "Allow an app associated with an &IAM; role to only read the current version of a secret",
    "Effect": "Allow",
    "Principal":  {"AWS": "arn:aws:iam::123456789012:role/EncryptionApp" },
    "Action": ["secretsmanager:GetSecret"],
    "Condition": { "ForAnyValue:StringEquals": {"secretsmanager:VersionStage": "AWSCURRENT" } },
    "Resource": "*"
}
```

[Suggest improvements to this example on GitHub\.](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/iam_policies/secretsmanager/asm-resource-policy-grant-role-for-app-access-to-awscurrent.json)

In the preceding policy statement, `arn:aws:iam::123456789012:role/EncryptionApp` refers to the IAM role named EncryptionApp in AWS account 123456789012\. Principals that can assume this role are allowed to perform the one action that's listed in the policy statement\. This action is to get the details for the current version of the secret\.

To learn all the different ways that you can specify a principal in a secret policy document, see [Specifying a Principal](http://docs.aws.amazon.com/IAM/latest/UserGuide/auth-and-access.xmlreference_policies_elements.html#Principal_specifying) in the *IAM User Guide*\.

To learn more about Secrets Manager secret policies, see [Resource\-based Policies](auth-and-access_overview.md#auth-and-access_resource-policies)\.

## Examining IAM Policies<a name="determine-acccess_examine-iam-policies"></a>

In addition to the secret policy, you can also use IAM policies in combination with the secret policy to allow access to a secret\. For more information about how IAM policies and secret policies work together, see [Understanding Policy Evaluation](#determine-acccess_understanding-policy-evaluation)\.

To determine which identities currently have access to a secret through IAM policies, you can use the browser\-based [IAM Policy Simulator](https://policysim.aws.amazon.com/) tool, or you can make requests to the IAM policy simulator API\.

**To examine the effect of policies**  
Follow the steps under one of the following tabs:

------
#### [ Using the web\-based Policy Simulator ]<a name="determine-acccess_examine-iam-policies_simulator"></a>

You can use the IAM Policy Simulator to help you learn which principals have access to a secret through an IAM policy\.

1. Sign in to the AWS Management Console, and then open the IAM Policy Simulator at [https://policysim\.aws\.amazon\.com/](https://policysim.aws.amazon.com/)\.

1. In the **Users, Groups, and Roles** pane, choose the user, group, or role whose policies you want to simulate\.

1. \(Optional\) Clear the check box next to any policies that you want to omit from the simulation\. To simulate all policies, leave all policies selected\.

1. In the **Policy Simulator** pane, do the following:

   1. For **Select service**, choose **AWS Secrets Manager**\.

   1. To simulate specific Secrets Manager actions, for **Select actions**, choose the actions to simulate\. To simulate all Secrets Manager actions, choose **Select All**\.

1. \(Optional\) The Policy Simulator simulates access to all secrets by default\. To simulate access to a specific secret, select **Simulation Settings**, and then type the Amazon Resource Name \(ARN\) of the secret to simulate\.

1. Select **Run Simulation**\.

You can view the results of the simulation in the **Results** section\. Repeat steps 2 through 6 for every IAM user, group, and role in the AWS account\.

------
#### [ Using the Policy Simulator CLI or SDK operations ]

You can use the IAM API to examine IAM policies programmatically\. The following steps provide a general overview of how to do this:

1. For each AWS account that's listed as a principal in the secret's policy \(that is, each *root user account* that's listed in this format: `"Principal": {"AWS": "arn:aws:iam::accountnumber:root"}`\), use the [ListUsers](http://docs.aws.amazon.com/IAM/latest/APIReference/API_ListUsers.html) and [ListRoles](http://docs.aws.amazon.com/IAM/latest/APIReference/API_ListRoles.html) operations in the IAM API to retrieve a list of every IAM user and role in that account\.

1. For each IAM user and role in the list, use the [SimulatePrincipalPolicy](http://docs.aws.amazon.com/IAM/latest/APIReference/API_SimulatePrincipalPolicy.html) operation in the IAM API, and pass the following parameters:
   + For `PolicySourceArn`, specify the ARN of a user or role from your list\. You can specify only one `PolicySourceArn` for each `SimulatePrincipalPolicy` API request\. So you must call this API multiple times, once for each IAM user and role in your list\.
   + For the `ActionNames` list, specify every Secrets Manager API action to simulate\. To simulate all Secrets Manager API actions, use `secretsmanager:*`\. To test individual Secrets Manager API actions, precede each API action with "`secretsmanager:`"—for example, "`secretsmanager:ListSecrets`"\. For a complete list of all Secrets Manager API actions, see [Actions](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_Operations.html) in the *AWS Key Management Service API Reference*\.
   + \(Optional\) To determine whether the IAM users or roles have access to specific secrets, use the `ResourceArns` parameter to specify a list of the ARNs of the secrets\. To determine whether the IAM users or roles have access to any secret, don't use the `ResourceArns` parameter\.

IAM responds to each `SimulatePrincipalPolicy` API request with an evaluation decision: `allowed`, `explicitDeny`, or `implicitDeny`\. For each response that contains an evaluation decision of `allowed`, the response also contains the name of the specific Secrets Manager API action that's allowed and, if applicable, the ARN of the secret that was used in the evaluation\.

------