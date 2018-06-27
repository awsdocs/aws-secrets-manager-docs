# Overview of Managing Access Permissions to Your Secrets Manager Secrets<a name="auth-and-access_overview"></a>

All AWS resources, including the secrets that you store in AWS Secrets Manager, are owned by an AWS account\. The permissions to create or access those resources are governed by permissions policies\. An account administrator can control access to AWS resources by attaching permissions policies directly to the resources \(the secrets\), or to the IAM identities \(users, groups, and roles\) that need to access the resources\.

**Note**  
An *administrator* \(or administrator user\) is a user who has administrator permissions\. This typically means that they have permissions to perform all operations on all resources for a service\. For more information, see [IAM Best Practices](http://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

An administrator for Secrets Manager in an account can perform administrative tasks, including delegating administrator permissions to other IAM users or roles in the account\. To do this, you attach an IAM permissions policy to an IAM user, group, or role\. By default, a user has no permissions at all\. This results in what we commonly refer to as an *implicit deny*\. The policy that you attach overrides the implicit deny with an *explicit allow* that specifies which actions the user can perform, and which resources they can perform the actions on\. The policy can be attached to users, groups, and roles within the account\. If the permissions are granted to a role, that role can be assumed by users in other accounts in the organization\.

**Topics**
+ [Authentication](#auth-and-access_authentication)
+ [Access Control \(Authorization\)](#auth-and-access_authorization)

## Authentication<a name="auth-and-access_authentication"></a>

Authentication is the process of establishing an identity that represents you in the services that you access\. Typically, an identity is assigned to you, and you access it by proving that you are who you say your are\. You do this by supplying "credentials" with your request, such as a user name and password—or by encrypting your request with an AWS access key\.

AWS supports the following types of identities:
+ **AWS account root user** – When you sign up for AWS, you provide an email address and password for your AWS account\. These are the *credentials* for your account root user, and they provide complete access to all of your AWS resources\.
**Important**  
For security reasons, we recommend that you use the root user credentials only to create an administrator user, which is an IAM user with full permissions to your AWS account\. Then you can use this administrator user to create other IAM users and roles with limited, specific permissions for a job or role\. For more information, see [Create Individual IAM Users \(IAM Best Practices\)](http://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#create-iam-users) and [Creating An Admin User and Group](http://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) in the *IAM User Guide*\.
+ **IAM user** – An [IAM user](http://docs.aws.amazon.com/IAM/latest/UserGuide/auth-and-access.xmlid_users.html) is an identity within your AWS account that has specific permissions \(for example, to access a Secrets Manager secret\)\. You can use an IAM user name and password to sign in to secure AWS webpages like the [AWS Management Console](https://console.aws.amazon.com/), [AWS Discussion Forums](https://forums.aws.amazon.com/auth-and-access.xml), or the [AWS Support Center](https://console.aws.amazon.com/support/home#/)\.

  In addition to a user name and password, you can also create [access keys](http://docs.aws.amazon.com/IAM/latest/UserGuide/auth-and-access.xmlid_credentials_access-keys.html) for each user to enable the user to access AWS services programmatically, through [one of the AWS SDKs](https://aws.amazon.com/tools/#sdk)or the [command line tools](https://aws.amazon.com/tools/#cli)\. The SDKs and command line tools use the access keys to cryptographically sign API requests\. If you don't use the AWS tools, you must sign API requests yourself\. AWS KMS supports *Signature Version 4*, an AWS protocol for authenticating API requests\. For more information about authenticating API requests, see [Signature Version 4 Signing Process](http://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) in the *AWS General Reference*\.
+ **IAM role** – An [IAM role](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) is another IAM identity you can create in your account that has specific permissions\. It's similar to an IAM user, but it's not associated with a specific person\. An IAM role enables you to obtain temporary access keys to access AWS services and resources programmatically\. AWS roles are useful in the following situations:
  + **Federated user access** – Instead of creating an IAM user, you can use pre\-existing user identities from [AWS Directory Service](https://aws.amazon.com/directoryservice/), your enterprise user directory, or a web identity provider \(IdP\)\. These are known as *federated users*\. Federated users are associated with IAM roles by an [identity provider](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers.html)\. For more information about federated users, see [Federated Users and Roles](http://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_access-management.html#intro-access-roles) in the *IAM User Guide*\.
  + **Cross\-account access** – You can use an IAM role in your AWS account to allow another AWS account permissions to access your account's resources\. For an example, see [Tutorial: Delegate Access Across AWS Accounts Using IAM Roles](http://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_cross-account-with-roles.html) in the *IAM User Guide*\.
  + **AWS service access** – You can use an IAM role in your account to grant an AWS service permissions to access your account's resources\. For example, you can create a role that allows Amazon Redshift to access an S3 bucket on your behalf, and then load data stored in the S3 bucket into an Amazon Redshift cluster\. For more information, see [Creating a Role to Delegate Permissions to an AWS Service](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide*\.
  + **Applications running on EC2 instances** – Instead of storing access keys on an EC2 instance \(for use by applications that run on the instance and make AWS API requests\), you can use an IAM role to provide temporary access keys for these applications\. To assign an IAM role to an EC2 instance, you create an *instance profile*, and then attach it when you launch the instance\. An instance profile contains the role, and enables applications running on the EC2 instance to get temporary access keys\. For more information, see [Using Roles for Applications on Amazon EC2](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html) in the *IAM User Guide*\.

After you sign in with an identity, you then use access control \(authorization\) to establish what you \(through your identity\) are allowed to do\.

## Access Control \(Authorization\)<a name="auth-and-access_authorization"></a>

You can have a valid identity with credentials to authenticate your requests, but you also need *permissions* to make Secrets Manager API requests to create, manage, or use Secrets Manager resources\. For example, you must have permissions to create a secret, to manage the secret, to retrieve the secret, and so on\. The following pages describe how to manage permissions for Secrets Manager\. 

### Secrets Manager Policies: Combining Resources and Actions<a name="auth-and-access_resources-and-ops"></a>

This section discusses how Secrets Manager concepts map to their IAM equivalent concepts\.

#### Policies<a name="auth-and-access_policy-types"></a>

Permissions in Secrets Manager, as in almost all AWS services, are granted by creating and attaching permission policies\. Policies come in two basic types:
+ **Identity\-based policies** – These are attached directly to a user, group, or role\. The policy specifies what the attached identity is allowed to do\. The attached user is automatically and implicitly the `Principal` of the policy\. You can specify the `Actions` that the identity can perform, and the `Resources` the identity can perform the actions on\. They enable you to:
  + Grant access to multiple resources that you want to share with the identity\.
  + Control access to APIs for resources that don't yet exist, such as the various `Create*` operations\.
  + Grant access to an IAM group to a resource\.
+ **Resource\-based policies** – These are attached directly to a resource—in this case, a secret\. The policy specifies who can access the secret and what actions they can perform against it\. The attached secret is automatically and implicitly the `Resource` of the policy\. You can specify the `Principals` that can access the secret and the `Actions` that the principals can perform\. They enable you to:
  + Grant access to multiple principals \(users or roles\) to a single secret\. Note that you *can't* specify an IAM group as a principal in a resource\-based policy\. Only users and roles are principals\.
  + Grant access to users or roles in other AWS accounts by specifying the account IDs in the `Principal` element of a policy statement\. A requirement for this "cross account" access to a secret is one of the primary reasons to use a resource\-based policy\.

Which type should you use? Although their functionality does overlap a great deal, there are scenarios where one has a distinct advantage over the other, and both should have a place in your security strategy\. When either policy type can work, choose the type that simplifies your maintenance for the policies as people come and go from your organization\.

**Important**  
When an IAM principal in one account accesses a secret that's in a different account, that secret must be encrypted by using a custom AWS KMS customer master key \(CMK\)\. A secret that's encrypted with the default Secrets Manager CMK for the account can be decrypted only by principals in that account\. A principal from a different account must be granted permission to both the secret and to the custom AWS KMS CMK\.   
As an alternative, you can grant the user cross\-account access to assume an IAM role in the same account as the secret\. Because the role is in the same account as the secret, it can access secrets that are encrypted with the default AWS KMS key for the account\.

#### Resources<a name="auth-and-access_resources"></a>

In Secrets Manager, **secrets** are the resources you can control access to\.

Each secret has a unique Amazon Resource Name \(ARN\) associated with it\. You can control access to a secret by specifying its ARN in the `Resource` element of an IAM permission policy\. The ARN of a secret looks like the following:

```
arn:aws:secretsmanager:<region>:<account-id>:secret:optional-path/secret-name-6-random-characters
```

##### Understanding Resource Ownership<a name="auth-and-access_ownership"></a>

The AWS account owns the resources that you create in the account, regardless of who created the resources\. Specifically, the resource owner is the AWS account of the root user, IAM user, or IAM role whose credentials authenticate the resource creation request\. The following examples illustrate how this works:
+ If you sign in as the root user account to create a secret, your AWS account is the owner of the resource\.
+ If you create an IAM user in your account and grant that user permissions to create a secret, the user can create a secret\. However, the AWS account that the user belongs to owns the secret\.
+ If you create an IAM role in your account with permissions to create a secret, anyone who can assume the role can create a secret\. The AWS account that the role \(not the assuming user\) belongs to owns the secret\.

#### Actions vs\. Operations<a name="auth-and-access_operations"></a>

Secrets Manager provides a set of *operations* \(API calls and CLI commands\) to work with secrets\. They enable you to do things like create, list, access, modify, or delete secrets\. These operations correspond to policy *actions* that you can use to grant or deny access to that operation\. Most of the time, there's a one\-to\-one relationship between API operations and the actions that you can assign in a policy\. To control access to an operation, specify its corresponding action in the `Action` element of an IAM policy\. For a list of Secrets Manager actions that can be used in a policy, see [Actions, Resources, and Context Keys You Can Use in an IAM Policy or Secret Policy for AWS Secrets Manager](reference_iam-permissions.md)\.
+ When you combine both an `Action` element and a `Resource` element in an *identity*\-based permission policy `Statement`, then you control both the actions that can be performed and the resources that they can be performed on\. The limits apply to the user, group, or role that the policy is attached to\.
+ When you combine both an `Action` element and a `Principal` element in a *resource*\-based permission policy `Statement`, then you control both the actions that can be performed—and the users, groups, or roles \(the principals\) that can perform those actions\. The limits apply to the secret that the policy is attached to\.

When you want to grant permissions to *create new secrets*, use an identity\-based policy that's attached to your users, groups, or roles\. This technique can also be helpful when you want to manage multiple secrets together in a similar manner\.

When you want to grant permissions to *access existing secrets*, you can use a resource\-based policy that's attached to the secret\.

### Managing Access to Resources with Policies<a name="auth-and-access_resource-access"></a>

A *permissions policy* describes *who* can perform *which actions* on *which resources*\. The following section explains the available options for creating permissions policies\. It briefly describes the elements that make up a policy, and the two types of policies that you can create for Secrets Manager\.

**Note**  
This section discusses using IAM in the context of Secrets Manager\. It doesn't provide detailed information about the IAM service\. For complete IAM documentation, see the [IAM User Guide](http://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)\. For information about IAM policy syntax and descriptions, see the [AWS IAM Policy Reference](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

It's important to understand that it doesn't really matter whether you use secret\-based or identity\-based permission policies\. The results are the same\. All of the policies that apply are grouped together and processed as if they were one large policy\. This basic rule structure controls how they interact:


|  | 
| --- |
| Explicit Deny >> Explicit Allow >> Implicit Deny \(default\) | 

For a request to perform an AWS operation on an AWS resource, the following rules apply:
+ **If any statement in any policy with an explicit "deny" matches the request's action and resource**: The explicit deny overrides everything else\. The specified actions on the specified resources are always blocked\.
+ **If there's no explicit "deny", but there's a statement with an explicit "allow" that matches the request's action and resource**: The explicit allow applies, which grants actions in that statement access to the resources in that statement\.
+ **If there's no statement with an explicit "deny" and no statement with an explicit "allow" that matches the request's action and resource**: The request is *implicitly* denied by default\.

It's important to understand that an *implicit* deny can be overridden with an explicit allow\. An *explicit* deny can't ever be overridden\.


**Effective Permissions When Multiple Policies Apply**  

| Policy A | Policy B | Effective Permissions | 
| --- | --- | --- | 
| Allows | Silent | Access allowed | 
| Allows | Allows | Access allowed | 
| Allows | Denies | Access denied | 
| Silent | Silent | Access denied | 
| Silent | Allows | Access allowed | 
| Silent | Denies | Access denied | 
| Denies | Silent | Access denied | 
| Denies | Allows | Access denied | 
| Denies | Denies | Access denied | 

Policy A and Policy B can be of any type: an IAM identity\-based policy that's attached to a user or role, or a resource\-based policy that's attached to the secret\. You can include the effect of additional policies by taking the effective permission of the first two policies \(the results\) and treating that like a new Policy A\. Then add the results of the third policy as Policy B, and repeat this for each additional policy that applies\.

**Topics**
+ [AWS Managed Policies](#combining-identity-resource-policies)
+ [Specifying Policy Statement Elements](#auth-and-access_policy-elements)
+ [Identity\-based Policies](#auth-and-access_iam-policies)
+ [Resource\-based Policies](#auth-and-access_resource-policies)

#### AWS Managed Policies<a name="combining-identity-resource-policies"></a>

AWS addresses many common use cases by providing standalone IAM policies that are created and administered by AWS\. These *managed policies* grant necessary permissions for common use cases so that you can avoid having to investigate which permissions are needed\. For more information, see [AWS Managed Policies](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

The following AWS managed policy, which you can attach to users in your account, is specific to Secrets Manager:
+ **[SecretsManagerReadWrite](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/SecretsManagerReadWrite)**–This policy can be attached to IAM users and roles that need to administer Secrets Manager\. It grants full permissions to the Secrets Manager service itself, and limited permissions to other services, such as AWS KMS, Amazon CloudFront, AWS Serverless Application Repository, and AWS Lambda\.

  You can view this policy in the [AWS Management Console](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/SecretsManagerReadWrite)\.
**Important**  
For security reasons, this managed policy does ***not*** include the IAM permissions that are needed to configure rotation\. You must explicitly grant those permissions separately\. Typically, you attach the [IAMFullAccess](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/IAMFullAccess) managed policy to a Secrets Manager administrator to enable them to configure rotation\.

One important advantage of using an AWS managed policy is that it's maintained by the Secrets Manager team\. If the rotation process is ever expanded to include new functionality that requires additional permissions, those permissions will be added to the managed policy at that time\. Your rotation functions will automatically receive the new permissions so that they continue to operate as expected without any interruption\.

#### Specifying Policy Statement Elements<a name="auth-and-access_policy-elements"></a>

This is a very brief overview of IAM permission policies from the perspective of Secrets Manager\. For more detail about IAM policy syntax, see the [AWS IAM Policy Reference](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

Secrets Manager defines a set of API operations that can interact with or manipulate a secret in some way\. To grant permissions for these operations, Secrets Manager defines a set of corresponding [actions](reference_iam-permissions.md) that you can specify in a policy\. For example, Secrets Manager defines actions that work on a secret, such as `CreateSecret`, `GetSecretalue`, `ListSecrets`, and `RotateSecret`\.

A policy document must have a `Version` element\. We recommend always using the latest version to ensure that you can use all of the available features\. As of this writing, the only available version is `2012-10-17` \(the latest version\)\. 

In addition, a secret policy document must have one `Statement` element with one or more statements in an array\. Each statement can consist of up to six elements:
+ **Sid** – \(Optional\) The `Sid` is a statement identifier, which is an arbitrary string that you can use to identify the statement\.
+ **Effect** – \(Required\) Use this keyword to specify whether the policy statement allows or denies the action on the resource\. If you don't explicitly allow access to a resource, access is *implicitly* denied\. You also can explicitly deny access to a resource\. You might do this to ensure that a user can't perform the specified action on the specified resource, even if a different policy grants access\. It's important to understand that when there are multiple statements that overlap, explicit deny in a statement overrides any other statements that explicitly allow\. Explicit allow statements override the implicit deny that's present by default\.
+ **Action** – \(Required\) Use this keyword to identify the actions that you want to allow or deny\. These actions usually, but not always, correspond one\-to\-one with the available operations\. For example, depending on the specified `Effect`, `secretsmanager:PutSecretValue` either allows or denies the user permissions to perform the Secrets Manager `PutSecretValue` operation\.
+ **Resource** – \(Required\) In an identity\-based policy that's attached to a user, group, or role, you use this keyword to specify the Amazon Resource Name \(ARN\) of the resource that the policy statement applies to\. If you don't want the statement to restrict access to a specific resource, then you can use "\*", and the resulting statement restricts only actions\. In a resource\-based policy that's attached to a secret, the resource must *always* be "\*"\.
+ **Principal** – \(Required in a resource\-based policy only\) For resource\-based policies that are attached directly to a secret, you specify the user, role, account, service, or other entity that you want to receive permissions\. This element isn't valid in an identity\-based policy\. This is because, in identity\-based policies, the user or role that the policy is attached to is automatically and implicitly the principal\.
+ **Condition** – \(Optional\) Use this keyword to specify additional conditions that must be true for the statement to "match" and the `Effect` to apply\. For more information, see [IAM JSON Policy Elements: Condition](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html)\.

#### Identity\-based Policies<a name="auth-and-access_iam-policies"></a>

You can attach policies to IAM identities\. For example, you can do the following:
+ **Attach a permissions policy to a user or a group in your account** – To grant a user permissions to create a secret, you can attach a permissions policy directly to the user or to a group that the user belongs to \(recommended\)\.
+ **Attach a permissions policy to a role** – You can attach a permissions policy to an IAM role to grant access to a secret to anyone who assumes the role\. This includes users in the following scenarios:
  + **Federated users** – You can grant permissions to users who are authenticated by an identity system other than IAM\. For example, you can associate IAM roles to mobile app users who sign in using Amazon Cognito\. The role grants the app temporary credentials with the permissions that are in the role's permission policy\. Those permissions can include access to a secret\. For more information, see [What is Amazon Cognito?](http://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html) in the *Amazon Cognito Developer Guide*\. 
  + **Applications running on EC2 instances** – You can grant permissions to applications that are running on an Amazon EC2 instance\. This is done by attaching an IAM role to the instance\. When an application in the instance wants to invoke an AWS API, it can get AWS temporary credentials from the instance metadata\. These temporary credentials are associated with a role, and are limited by the role's permissions policy\. Those permissions can include access to a secret\.
  + **Cross\-account access** – An administrator in account A can create a role to grant permissions to a user in a different account B\. For example:

    1. The account A administrator creates an IAM role and attaches a permissions policy to the role\. The policy grants access to secrets in account A and specifies what users can do with them\.

    1. The account A administrator attaches a trust policy to the role that identifies the account ID of account B in the `Principal` element to specify who can assume the role\.

    1. The account B administrator can then delegate permissions to any users in account B that enable them to assume account A's role\. Doing this allows users in account B to access the secrets in the first account\. 

For more information about using IAM to delegate permissions, see [Access Management](http://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) in the *IAM User Guide*\.

The following example policy can be attached to a user, group, or role\. It allows the affected user or role to perform the `DescribeSecret` and `GetSecretValue` operations in your account only on secrets whose name begin with the path "TestEnv/"\. The user or role is also restricted to retrieving only the version of the secret that has the staging label `AWSCURRENT` attached\. Replace the *<region>* and *<account\_id>* placeholders in the following examples with your actual values\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid" : "Stmt1DescribeSecret",  
      "Effect": "Allow",
      "Action": [ "secretsmanager:DescribeSecret" ],
      "Resource": "arn:aws:secretsmanager:<region>:<account_id>:secret:TestEnv/*"
    },
    {
      "Sid" : "Stmt2GetSecretValue",  
      "Effect": "Allow",
      "Action": [ "secretsmanager:GetSecretValue" ],
      "Resource": "arn:aws:secretsmanager:<region>:<account_id>:secret:TestEnv/-*",
      "Condition" : { 
        "ForAnyValue:StringLike" : {
          "secretsmanager:Label" : "AWSCURRENT" 
        } 
      }
    }
  ]
}
```

Because a secret version can have multiple staging labels attached, you need to use the [IAM policy language's "set operators"](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_multi-value-conditions.html) to compare them in a policy\. In the previous example, `ForAnyValue:StringLike` says that if any one of the staging labels that are attached to the secret version being evaluated matches the string "`AWSCURRENT`", then the statement matches, and the `Effect` is applied\.

For more example identity\-based policies, see [Using Identity\-Based Policies \(IAM Policies\) for AWS Secrets Manager](auth-and-access_identity-based-policies.md)\. For more information about users, groups, roles, and permissions, see [Identities \(Users, Groups, and Roles\)](http://docs.aws.amazon.com/IAM/latest/UserGuide/id.html) in the *IAM User Guide*\.

#### Resource\-based Policies<a name="auth-and-access_resource-policies"></a>

Each Secrets Manager secret can have one resource\-based permissions policy \(a *secret policy*\) attached to it\. You can use a secret policy to grant cross\-account permissions as an alternative to using identity\-based policies with IAM roles\. For example, you can grant permissions to a user in account B to access your secret in account A by simply adding permissions to the secret policy, and identifying the user in account B as a `Principal`—instead of creating an IAM role\.

The following is an example Secrets Manager secret policy that has one statement\. The statement allows the administrator of account 123456789012 to delegate permissions to users and roles in that account\. The permissions that the administrator can delegate are limited to the `secretsmanager:GetSecret` action on a single secret named "prod/ServerA\-a1b2c3"\. The condition ensures that the user can retrieve only the current version of the secret\.

```
{
  "Version" : "2012-10-17",
  "Statement" : [
    {
      "Effect": "Allow",
      "Principal": {"AWS": "arn:aws:iam::123456789012:root" },
      "Action": "secretsmanager:GetSecretValue",
      "Resource": "arn:aws:secretsmanager:<region>:<account_id>:secret:prod/ServerA-a1b2c3",
      "Condition": {
        "ForAnyValue:StringEquals": {
          "secretsmanager:VersionStage" : "AWSCURRENT"
        }
      }
    }
  ]
}
```

**Important**  
When an IAM principal from one account accesses a secret that's in a different account, that secret must be encrypted by using a custom AWS KMS CMK\. A secret that's encrypted with the default Secrets Manager CMK for the account can be decrypted only by principals in that account\. A principal from a different account must be granted permission to both the secret and to the custom AWS KMS CMK\.   
As an alternative, you can grant the user cross\-account access to assume an IAM role in the same account as the secret\. Because the role is in the same account as the secret, it can access secrets that are encrypted with the default AWS KMS key for the account\.

Because a secret version can have multiple staging labels attached, you need to use the [IAM policy language's set operators](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_multi-value-conditions.html) to compare them in a policy\. In the previous example, `ForAnyValue:StringLike` says that if any one of the labels that are attached to the version being evaluated matches "`AWSCURRENT`", then the statement matches, and the `Effect` is applied\.

For more example resource\-based policies with Secrets Manager, see [Using Resource\-based Policies for Secrets Manager](auth-and-access_resource-based-policies.md)\. For additional information about using resource\-based policies instead of IAM roles \(identity\-based policies\), see [How IAM Roles Differ from Resource\-based Policies](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_compare-resource-policies.html) in the *IAM User Guide*\.