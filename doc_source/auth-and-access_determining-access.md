# Determining Access to a Secret<a name="auth-and-access_determining-access"></a>

To determine the full extent of who or what currently has access to a secret in AWS Secrets Manager, you must examine all AWS Identity and Access Management \(IAM\) policies that are attached to either the IAM user and its groups or the IAM role\. You might do this to determine the scope of potential usage of a secret, or to help you meet compliance or auditing requirements\. The following topics can help you generate a complete list of the AWS principals \(identities\) that currently have access to a secret\.

**Topics**
+ [Understanding Policy Evaluation](#determine-acccess_understanding-policy-evaluation)
+ [Examining IAM Policies](#determine-acccess_examine-iam-policies)

## Understanding Policy Evaluation<a name="determine-acccess_understanding-policy-evaluation"></a>

When authorizing access to a secret, Secrets Manager evaluates all IAM policies attached to the IAM user or role making the request\. In many cases, Secrets Manager must evaluate the IAM policies together to determine whether access to the secret is allowed or denied\. To do this, Secrets Manager uses a process similar to the one described at [Determining Whether a Request is Allowed or Denied](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html#policy-eval-denyallow) in the *IAM User Guide*\.

For example, assume that you have two secrets and three users, all in the same AWS account\. The secrets and users have the following policies:
+ Ana has no IAM policy that references Secret 1 or Secret 2\.
+ Bob's IAM policy allows all Secrets Manager actions for all secrets\.
+ Carlos' IAM policy denies all Secrets Manager actions for all secrets\.

Ana cannot access either secret because there is no policy that explicitly allow her access\.

Bob can access both Secret 1 and Secret 2 because Bob has an IAM policy that allows access to all secrets in the account and there is no explicit deny anywhere to override that\.

Carlos cannot access Secret 1 or Secret 2 because all Secrets Manager actions are denied in his IAM policy\. The explicit deny in Carlos' IAM policy overrides any explicit allow found in any other policy\.

In summary, all policy statements from the identity that "allow" access are added together\. Any explicit deny overrides any allow to the overlapping action and resource\.

## Examining IAM Policies<a name="determine-acccess_examine-iam-policies"></a>

To determine which identities currently have access to a secret through IAM policies, you can use the browser\-based [IAM Policy Simulator](https://policysim.aws.amazon.com/) tool, or you can make requests to the IAM policy simulator API\.

**Topics**
+ [Examining IAM Policies with the IAM Policy Simulator](#determine-acccess_examine-iam-policies_simulator)
+ [Examining IAM Policies with the IAM API](#determine-acccess_examine-iam-policies_api)

### Examining IAM Policies with the IAM Policy Simulator<a name="determine-acccess_examine-iam-policies_simulator"></a>

The IAM Policy Simulator can help you learn which principals have access to a secret through an IAM policy\.

**To use the IAM Policy Simulator to determine access to a KMS secret**

1. Sign in to the AWS Management Console and then open the IAM Policy Simulator at [https://policysim\.aws\.amazon\.com/](https://policysim.aws.amazon.com/)\.

1. In the **Users, Groups, and Roles** pane, choose the user, group, or role whose policies you want to simulate\.

1. \(Optional\) Clear the check box next to any policies that you want to omit from the simulation\. To simulate all policies, leave all policies selected\.

1. In the **Policy Simulator** pane, do the following:

   1. For **Select service**, choose **AWS Secrets Manager**\.

   1. To simulate specific Secrets Manager actions, for **Select actions**, choose the actions to simulate\. To simulate all Secrets Manager actions, choose **Select All**\.

1. \(Optional\) The Policy Simulator simulates access to all secrets by default\. To simulate access to a specific secret, select **Simulation Settings** and then type the Amazon Resource Name \(ARN\) of the secret to simulate\.

1. Select **Run Simulation**\.

You can view the results of the simulation in the **Results** section\. Repeat steps 2 through 6 for every IAM user, group, and role in the AWS account\.

### Examining IAM Policies with the IAM API<a name="determine-acccess_examine-iam-policies_api"></a>

You can use the IAM API to examine IAM policies programmatically\. The following steps provide a general overview of how to do this:

1. For each AWS account listed as a principal in the secret's policy \(that is, each *root account* listed in this format: `"Principal": {"AWS": "arn:aws:iam::accountnumber:root"}`\), use the [ListUsers](http://docs.aws.amazon.com/IAM/latest/APIReference/API_ListUsers.html) and [ListRoles](http://docs.aws.amazon.com/IAM/latest/APIReference/API_ListRoles.html) operations in the IAM API to retrieve a list of every IAM user and role in that account\.

1. For each IAM user and role in the list, use the [SimulatePrincipalPolicy](http://docs.aws.amazon.com/IAM/latest/APIReference/API_SimulatePrincipalPolicy.html) operation in the IAM API, passing in the following parameters:
   + For `PolicySourceArn`, specify the Amazon Resource Name \(ARN\) of a user or role from your list\. You can specify only one `PolicySourceArn` for each `SimulatePrincipalPolicy` API request, so you must call this API multiple times, once for each IAM user and role in your list\.
   + For the `ActionNames` list, specify every AWS Secrets Manager API action to simulate\. To simulate all Secrets Manager API actions, use `secretsmanager:*`\. To test individual Secrets Manager API actions, precede each API action with "`secretsmanager:`", for example "`secretsmanager:ListSecrets`"\. For a complete list of all Secrets Manager API actions, see [Actions](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_Operations.html) in the *AWS Key Management Service API Reference*\.
   + \(Optional\) To determine whether the IAM users or roles have access to specific secrets, use the `ResourceArns` parameter to specify a list of the Amazon Resource Names \(ARNs\) of the secrets\. To determine whether the IAM users or roles have access to any secret, do not use the `ResourceArns` parameter\.

IAM responds to each `SimulatePrincipalPolicy` API request with an evaluation decision: `allowed`, `explicitDeny`, or `implicitDeny`\. For each response that contains an evaluation decision of `allowed`, the response will also contain the name of the specific Secrets Manager API action that is allowed and, if applicable, the ARN of the secret that was used in the evaluation\.