# Determining access to a secret<a name="auth-and-access_determining-access"></a>

To determine the full extent of users with access to a secret in AWS Secrets Manager, you must examine the secret policy, if one exists, and all AWS Identity and Access Management \(IAM\) policies attached to either the IAM user and the groups, or the IAM role\. You might do this to determine the scope of potential usage of a secret, or to help you meet compliance or auditing requirements\. The following topics help you generate a complete list of the AWS principals and identities with current access to a secret\.

**Topics**
+ [Understanding policy evaluation](determine-acccess_understanding-policy-evaluation.md)
+ [Examining the resource policy](determine-acccess_examine-resource-policy.md)
+ [Examining IAM policies](determine-acccess_examine-iam-policies.md)