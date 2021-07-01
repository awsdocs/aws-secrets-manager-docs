# Understanding resource ownership<a name="auth-and-access_ownership"></a>

The AWS account owns the resources you create in the account, regardless of who created the resources\. Specifically, the resource owner is the AWS account of the root user, IAM user, or IAM role with credentials to authenticate the resource creation request\. The following examples illustrate how this works:
+ If you sign in as the root user account to create a secret, your AWS account becomes the owner of the resource\.
+ If you create an IAM user in your account and grant the user permissions to create a secret, the user can create a secret\. However, the AWS account of the user owns the secret\.
+ If you create an IAM role in your account with permissions to create a secret, anyone who can assume the role can create a secret\. The AWS account of the role, not the assuming user, owns the secret\.