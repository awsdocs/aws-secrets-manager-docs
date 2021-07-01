# Overview of managing access permissions to your Secrets Manager secrets<a name="auth-and-access_overview"></a>

An AWS account owns all of AWS resources, including the secrets you store in AWS Secrets Manager\. AWS contains permissions policies to create or access the resources\. An account administrator can control access to AWS resources by attaching permissions policies directly to the resources, the secrets, or to the IAM identities, users, groups, and roles, needing to access the resources\.

**Note**  
An *administrator*, or administrator user, has administrator permissions\. This typically means they have permissions to perform all operations on all resources for a service\. For more information, see [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

An account administrator for Secrets Manager can perform administrative tasks, including delegating administrator permissions to other IAM users or roles in the account\. To do this, you attach an IAM permissions policy to an IAM user, group, or role\. By default, a user has no permissions at all, which means the user has an *implicit deny*\. The policy you attach overrides the implicit deny with an *explicit allow* specifying actions the user can perform, and on which resources they can perform the actions \. The policy can be attached to users, groups, and roles within the account\. If you grant the permissions to a role, the role can be assumed by users in other accounts in the organization\.

**Topics**
+ [Authentication](auth-and-access_authentication.md)
+ [Access control and authorization](auth-and-access_authorization.md)