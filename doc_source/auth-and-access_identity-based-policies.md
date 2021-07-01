# Using identity\-based policies \(IAM policies\) and ABAC for Secrets Manager<a name="auth-and-access_identity-based-policies"></a>

As the administrator of an account, you can control access to the AWS resources in your account by attaching permissions policies to IAM identities such as users, groups, and roles\. When granting permissions, you decide the permissions, the resources, and the specific actions to allow on those resources\. If you grant permissions to a role, users in other accounts you specify can assume that role\.

By default, a user or role has no permissions of any kind\. You must explicitly grant any permissions by using a policy\. If you don't explicitly grant permission, then the permission implicitly denies\. If you explicitly deny a permission, then that overrules any other policy allowing it\. In other words, a user has only permissions explicitly granted and not explicitly denied\.



For an overview of the basic elements for policies, see [Managing access to resources with policies](auth-and-access_resource-access.md)\.

You can also implement attribute\-based access control \(ABAC\) for your IAM policies\. Attribute\-based access control \(ABAC\) is an authorization strategy that defines permissions based on attributes\. In AWS, these attributes are called tags\. Tags can be attached to IAM principals \(users or roles\) and to AWS resources\. You can create a single ABAC policy or small set of policies for your IAM principals\. These ABAC policies can be designed to allow operations when the principal's tag matches the resource tag\. ABAC is helpful in environments that grow rapidly and helps with situations where policy management becomes cumbersome\. 

For more information about ABAC, see [What is ABAC for AWS?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_attribute-based-access-control.html)