# Attach a permissions policy to an identity<a name="auth-and-access_iam-policies"></a>

You can attach permissions policies to [IAM identities: users, user groups, and roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html)\. In an identity\-based policy, you specify which secrets the identity can access and the actions the identity can perform on the secrets\. For more information, see [Adding and removing IAM identity permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html)\.

You can grant permissions to a role that represents an application or user in another service\. For example, an application running on an Amazon EC2 instance might need access to a database\. You can create an IAM role attached to the EC2 instance profile and then use a permissions policy to grant the role access to the secret that contains credentials for the database\. For more information, see [Using an IAM role to grant permissions to applications running on Amazon EC2 instances](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html)\. Other services that you can attach roles to include [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/dg/c-getting-started-using-spectrum-add-role.html), [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/lambda-permissions.html), and [Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html)\.

You can also grant permissions to users authenticated by an identity system other than IAM\. For example, you can associate IAM roles to mobile app users who sign in with Amazon Cognito\. The role grants the app temporary credentials with the permissions in the role permission policy\. Then you can use a permissions policy to grant the role access to the secret\. For more information, see [Identity providers and federation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers.html)\.

You can use identity\-based policies to:
+ Grant an identity access to multiple secrets\.
+ Control who can create new secrets, and who can access secrets that haven't been created yet\.
+ Grant an IAM group access to secrets\.

For more information, see [Permissions policy examples](auth-and-access_examples.md)\.
