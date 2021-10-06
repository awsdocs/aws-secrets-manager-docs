# Attach a permissions policy to an identity<a name="auth-and-access_iam-policies"></a>

You can attach permissions policies to [IAM identities: users, user groups, and roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html)\. In an identity\-based policy, you specify which secrets the identity can access and the actions the identity can perform on the secrets\. 

You can use identity\-based policies to:
+ Grant an identity access to multiple secrets\.
+ Control who can create new secrets, and who can access secrets that haven't been created yet\.
+ Grant an IAM group access to secrets\.

See [Permissions policy examples](auth-and-access_examples.md)\.

**To add or remove permissions on an identity**
+ Do one of the following:
  + To use the console, see [Adding IAM identity permissions \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html#add-policies-console)\.
  + To use the AWS CLI, see [Adding IAM identity permissions \(AWS CLI\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html#add-policy-cli)
  + To use the AWS API, see [Adding IAM identity permissions \(AWS API\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html#add-policy-api)