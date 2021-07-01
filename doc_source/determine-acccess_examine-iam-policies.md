# Examining IAM policies<a name="determine-acccess_examine-iam-policies"></a>

In addition to the secret policy, you can also use IAM policies in combination with the secret policy to allow access to a secret\. For more information about how IAM policies and secret policies work together, see [Understanding policy evaluation](determine-acccess_understanding-policy-evaluation.md)\.

To determine the identities with current access to a secret through IAM policies, you can use the browser\-based [IAM Policy Simulator](https://policysim.aws.amazon.com/) tool, or you can send requests to the IAM policy simulator API\.

**Examining the effect of policies**  
Follow the steps under one of the following tabs:

------
#### [ Using the web\-based Policy Simulator ]<a name="determine-acccess_examine-iam-policies_simulator"></a>

You can use the IAM Policy Simulator to help you learn which principals have access to a secret through an IAM policy\.

1. Sign in to the AWS Management Console, and then open the IAM Policy Simulator at [https://policysim\.aws\.amazon\.com/](https://policysim.aws.amazon.com/)\.

1. In the **Users, Groups, and Roles** pane, choose the user, group, or role with policies you want to simulate\.

1. \(Optional\) Clear the check box next to any policies you want to omit from the simulation\. To simulate all policies, leave all policies selected\.

1. In the **Policy Simulator** pane, do the following:

   1. For **Select service**, choose **AWS Secrets Manager**\.

   1. To simulate specific Secrets Manager actions, for **Select actions**, choose the actions to simulate\. To simulate all Secrets Manager actions, choose **Select All**\.

1. \(Optional\) The Policy Simulator simulates access to all secrets by default\. To simulate access to a specific secret, select **Simulation Settings**, and then type the Amazon Resource Name \(ARN\) of the secret to simulate\.

1. Select **Run Simulation**\.

You can view the results of the simulation in the **Results** section\. Repeat steps 2 through 6 for every IAM user, group, and role in the AWS account\.

------
#### [ Using the Policy Simulator CLI or SDK operations ]

You can use the IAM API to examine IAM policies programmatically\. The following steps provide a general overview of how to do this:

1. For each AWS account listed as a principal in the secret policy, that is, each *root user account* listed in this format: `"Principal": {"AWS": "arn:aws:iam::accountnumber:root"}`, use the [ListUsers](https://docs.aws.amazon.com/IAM/latest/APIReference/API_ListUsers.html) and [ListRoles](https://docs.aws.amazon.com/IAM/latest/APIReference/API_ListRoles.html) operations in the IAM API to retrieve a list of every IAM user and role in the account\.

1. For each IAM user and role in the list, use the [SimulatePrincipalPolicy](https://docs.aws.amazon.com/IAM/latest/APIReference/API_SimulatePrincipalPolicy.html) operation in the IAM API, and send the following parameters:
   + For `PolicySourceArn`, specify the ARN of a user or role from your list\. You can specify only one `PolicySourceArn` for each `SimulatePrincipalPolicy` API request\. So you must call this API multiple times, once for each IAM user and role in your list\.
   + For the `ActionNames` list, specify every Secrets Manager API action to simulate\. To simulate all Secrets Manager API actions, use `secretsmanager:*`\. To test individual Secrets Manager API actions, precede each API action with "`secretsmanager:`"—for example, "`secretsmanager:ListSecrets`"\. For a complete list of all Secrets Manager API actions, see [Actions](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_Operations.html) in the *AWS Key Management Service API Reference*\.
   + \(Optional\) To determine if the IAM users or roles have access to specific secrets, use the `ResourceArns` parameter to specify a list of the ARNs of the secrets\. To determine whether the IAM users or roles have access to any secret, don't use the `ResourceArns` parameter\.

IAM responds to each `SimulatePrincipalPolicy` API request with an evaluation decision: `allowed`, `explicitDeny`, or `implicitDeny`\. For each response that contains an evaluation decision of `allowed`, the response also contains the name of the specific Secrets Manager API action allowed and, if applicable, the ARN of the secret used in the evaluation\.

------