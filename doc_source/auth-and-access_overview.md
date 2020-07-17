# Overview of Managing Access Permissions to Your Secrets Manager Secrets<a name="auth-and-access_overview"></a>

An AWS account owns all of AWS resources, including the secrets you store in AWS Secrets Manager\. AWS contains permissions policies to create or access the resources\. An account administrator can control access to AWS resources by attaching permissions policies directly to the resources, the secrets, or to the IAM identities, users, groups, and roles, needing to access the resources\.

**Note**  
An *administrator*, or administrator user, has administrator permissions\. This typically means they have permissions to perform all operations on all resources for a service\. For more information, see [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

An account administrator for Secrets Manager can perform administrative tasks, including delegating administrator permissions to other IAM users or roles in the account\. To do this, you attach an IAM permissions policy to an IAM user, group, or role\. By default, a user has no permissions at all, which means the user has an *implicit deny*\. The policy you attach overrides the implicit deny with an *explicit allow* specifying actions the user can perform, and on which resources they can perform the actions \. The policy can be attached to users, groups, and roles within the account\. If you grant the permissions to a role, the role can be assumed by users in other accounts in the organization\.

**Topics**
+ [Authentication](#auth-and-access_authentication)
+ [Access Control and Authorization](#auth-and-access_authorization)

## Authentication<a name="auth-and-access_authentication"></a>

AWS defines authentication as the process of establishing an identity representing you in the services you access\. Typically, an administrator assigns an identity to you, and you access the identity by providing credentials with your request, such as a user name and password—or by encrypting your request with an AWS access key\.

AWS supports the following types of identities:
+ **AWS account root user** – When you sign up for AWS, you provide an email address and password for your AWS account\. AWS uses this information as the *credentials* for your account root user, and provides complete access to all of your AWS resources\.
**Important**  
For security reasons, we recommend you use the root user credentials only to create an administrator user, an IAM user with full permissions to your AWS account\. Then you can use this administrator user to create other IAM users and roles with limited, specific permissions for a job or role\. For more information, see [Create Individual IAM Users \(IAM Best Practices\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#create-iam-users) and [Creating An Admin User and Group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) in the *IAM User Guide*\.
+ **IAM user** – [IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html) has an identity within your AWS account with specific permissions, for example, accessing a Secrets Manager secret\. You can use an IAM user name and password to sign in to secure AWS web pages such as the [AWS Management Console](https://console.aws.amazon.com/), [AWS Discussion Forums](https://forums.aws.amazon.com/), or the [AWS Support Center](https://console.aws.amazon.com/support/home#/)\.

  In addition to a user name and password, you can also create [access keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) for each user to enable t access to AWS services programmatically, through [one of the AWS SDKs](https://aws.amazon.com/tools/#sdk)or the [command line tools](https://aws.amazon.com/tools/#cli)\. The SDKs and command line tools use the access keys to cryptographically sign API requests\. If you don't use the AWS tools, you must sign API requests yourself\. AWS KMS supports *Signature Version 4*, an AWS protocol for authenticating API requests\. For more information about authenticating API requests, see [Signature Version 4 Signing Process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) in the *AWS General Reference*\.
+ **IAM role** – You can create an [IAM role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) in your account with specific permissions\. Similar to an IAM user, the IAM role does not associate with a specific person\. An IAM role enables you to obtain temporary access keys to access AWS services and resources programmatically\. AWS roles become useful in the following situations:
  + **Federated user access** – Instead of creating an IAM user, you can use pre\-existing user identities from [AWS Directory Service](https://aws.amazon.com/directoryservice/), your enterprise user directory, or a web identity provider \(IdP\)\. These are known as *federated users*\. [Identity providers](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers.html) associate IAM roles with federated users\.\. For more information about federated users, see [Federated Users and Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_access-management.html#intro-access-roles) in the *IAM User Guide*\.
  + **Cross\-account access** – You can use an IAM role in your AWS account to allow another AWS account permission to access your account resources\. For an example, see [Tutorial: Delegate Access Across AWS Accounts Using IAM Roles](http://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_cross-account-with-roles.html) in the *IAM User Guide*\.
  + **AWS service access** – You can use an IAM role in your account to grant an AWS service permission to access your account resources\. For example, you can create a role that allows Amazon Redshift to access an S3 bucket on your behalf, and then load data stored in the S3 bucket into an Amazon Redshift cluster\. For more information, see [Creating a Role to Delegate Permissions to an AWS Service](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide*\.
  + **Applications running on EC2 instances** – Instead of storing access keys on an EC2 instance, for use by applications running on the instance and sending AWS API requests, you can use an IAM role to provide temporary access keys for these applications\. To assign an IAM role to an EC2 instance, you create an *instance profile*, and then attach the profile when you launch the instance\. An instance profile contains the IAM role, and enables applications running on the EC2 instance to obtain temporary access keys\. For more information, see [Using Roles for Applications on Amazon EC2](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html) in the *IAM User Guide*\.

After you sign in with an identity, you then use access control and authorization, to establish tasks you, through your identity, can perform\.

## Access Control and Authorization<a name="auth-and-access_authorization"></a>

You can have a valid identity with credentials to authenticate your requests, but you also need *permissions* to make Secrets Manager API requests to create, manage, or use Secrets Manager resources\. For example, you must have permissions to create a secret, to manage the secret, to retrieve the secret, as well as other tasks\. The following sections describe managing permissions for Secrets Manager\. 

### Secrets Manager Policies: Combining Resources and Actions<a name="auth-and-access_resources-and-ops"></a>

This section discusses how Secrets Manager concepts map to the IAM equivalent concepts\.

#### Policies<a name="auth-and-access_policy-types"></a>

Secrets Manager grants permissions , as in almost all AWS services, by creating and attaching permission policies\. Policies have two basic types:
+ **Identity\-based policies** – Attached directly to a user, group, or role\. The policy specifies the allowed tasks for the attached identity\. The attached user automatically and implicitly becomes the `Principal` of the policy\. You can specify the `Actions` the identity can perform, and the `Resources` the identity can perform actions\. The policies enable you to perform the following actions:
  + Grant access to multiple resources to share with the identity\.
  + Control access to APIs for nonexistent resources, such as the various `Create*` operations\.
  + Grant access to an IAM group to a resource\.
+ **Resource\-based policies** – Attached directly to a resource—in this case, a secret\. The policy specifies access to the secret and the actions the user performs on the secret\. The attached secret automatically and implicitly becomes the `Resource` of the policy\. You can specify the `Principals`accessing the secret and the `Actions` the principals can perform\. The policies enable you to perform the following actions:
  + Grant access to multiple principals \(users or roles\) to a single secret\. Note that you *can't* specify an IAM group as a principal in a resource\-based policy\. Only users and roles can be principals\.
  + Grant access to users or roles in other AWS accounts by specifying the account IDs in the `Principal` element of a policy statement\. If you require a "cross account" access to a secret , then this might be one of the primary reasons to use a resource\-based policy\.

Which type should you use? Although their functionality does overlap a great deal, scenarios exist where one has a distinct advantage over the other, and both should have a place in your security strategy\. While either policy type can work, choose the type that simplifies your maintenance for the policies as people come and go from your organization\.

**Important**  
When an IAM principal in one account accesses a secret in a different account, the secret must be encrypted using a custom AWS KMS customer master key \(CMK\)\. A secret encrypted with the default Secrets Manager CMK for the account can be decrypted only by principals in that account\. A principal from a different account must be granted permission to both the secret and to the custom AWS KMS CMK\.   
As an alternative, you can grant cross\-account access to a user and assume an IAM role in the same account as the secret\. Because the role exists in the same account as the secret, the role can access secrets encrypted with the default AWS KMS key for the account\.

#### Resources<a name="auth-and-access_resources"></a>

In Secrets Manager, you control access to your **secrets**, 

Each secret has a unique Amazon Resource Name \(ARN\) associated with it\. You can control access to a secret by specifying the ARN in the `Resource` element of an IAM permission policy\. The ARN of a secret has the following format:

```
arn:aws:secretsmanager:<region>:<account-id>:secret:optional-path/secret-name-6-random-characters
```

##### Understanding Resource Ownership<a name="auth-and-access_ownership"></a>

The AWS account owns the resources you create in the account, regardless of who created the resources\. Specifically, the resource owner is the AWS account of the root user, IAM user, or IAM role with credentials to authenticate the resource creation request\. The following examples illustrate how this works:
+ If you sign in as the root user account to create a secret, your AWS account becomes the owner of the resource\.
+ If you create an IAM user in your account and grant the user permissions to create a secret, the user can create a secret\. However, the AWS account of the user owns the secret\.
+ If you create an IAM role in your account with permissions to create a secret, anyone who can assume the role can create a secret\. The AWS account of the role, not the assuming user, owns the secret\.

#### Actions vs\. Operations<a name="auth-and-access_operations"></a>

Secrets Manager provides a set of *operations* \(API calls and CLI commands\) to work with secrets\. The operations enable you to perform actions such as creating, listing, accessing, modifying, or deleting secrets\. These operations correspond to policy *actions* you can use to grant or deny access to that operation\. In most cases, a one\-to\-one relationship exists between API operations and the actions you can assign in a policy\. To control access to an operation, specify the corresponding action in the `Action` element of an IAM policy\. For a list of allowed Secrets Manager actions used in a policy, see [Actions, Resources, and Context Keys You Can Use in an IAM Policy or Secret Policy for AWS Secrets Manager](reference_iam-permissions.md)\.
+ When you combine both an `Action` element and a `Resource` element in an *identity*\-based permission policy `Statement`, then you control both the performed actions and the resources\. The limits apply to the user, group, or role of the attached the policy\. \.
+ When you combine both an `Action` element and a `Principal` element in a *resource*\-based permission policy `Statement`, then you control both actions that can be performed—and the users, groups, or roles \(the principals\) performing those actions\. The limits apply to the secret with the attached policy\.

When you want to grant permissions to *create new secrets*, use an identity\-based policy attached to your users, groups, or roles\. This technique can also be helpful when you want to manage multiple secrets together in a similar manner\.

When you want to grant permissions to *access existing secrets*, you can use a resource\-based policy attached to the secret\.

### Managing Access to Resources with Policies<a name="auth-and-access_resource-access"></a>

A *permissions policy* describes *who* can perform *which actions* on *which resources*\. The following section explains the available options for creating permissions policies, and also briefly describes the elements making up a policy, and the two types of policies you can create for Secrets Manager\.

**Note**  
This section discusses using IAM in the context of Secrets Manager, but does not provide detailed information about the IAM service\. For complete IAM documentation, see the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)\. For information about IAM policy syntax and descriptions, see the [AWS IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

You should understand you use either secret\-based or identity\-based permission policies for Secrets Manager \. Secrets Manager groups together all of the applied policies and processes as one large policy\. This basic rule structure controls the interaction:


|  | 
| --- |
| Explicit Deny >> Explicit Allow >> Implicit Deny \(default\) | 

For a request to perform an AWS operation on an AWS resource, the following rules apply:
+ **If any statement in any policy with an explicit "deny" matches the request action and resource**: The explicit deny overrides everything else and blocks the specified actions on the specified resources \.
+ **If no explicit "deny", but a statement with an explicit "allow" matches the request action and resource**: The explicit allow applies, which grants actions in that statement access to the resources in that statement\.
+ **If no statement with an explicit "deny" and no statement with an explicit "allow" matches the request action and resource**: AWS *implicitly* denies the request by default\.

You should understand that an *implicit* deny can be overridden with an explicit allow\. An *explicit* deny can't be overridden\.


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

Policy A and Policy B can be of any type: an IAM identity\-based policy attached to a user or role, or a resource\-based policy attached to the secret\. You can include the effect of additional policies by taking the effective permission of the first two policies \(the results\) and treating that like a new Policy A\. Then add the results of the third policy as Policy B, and repeat this for each additional policy that applies\.

**Topics**
+ [AWS Managed Policies](#combining-identity-resource-policies)
+ [Specifying Policy Statement Elements](#auth-and-access_policy-elements)
+ [Identity\-based Policies](#auth-and-access_iam-policies)
+ [Resource\-based Policies](#auth-and-access_resource-policies)

#### AWS Managed Policies<a name="combining-identity-resource-policies"></a>

AWS addresses many common use cases by providing standalone IAM policies created and administered by AWS\. These *managed policies* grant necessary permissions for common use cases so you can avoid investigating the necessary permissions\. For more information, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

Secrets Manager has a specific AWS managed policy, which you can attach to users in your account:
+ **[SecretsManagerReadWrite](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/SecretsManagerReadWrite)**–This policy can be attached to IAM users and roles to administer Secrets Manager\. The policy grants full permissions to the Secrets Manager service , and limited permissions to other services, such as AWS KMS, Amazon CloudFront, AWS Serverless Application Repository, and AWS Lambda\.

  You can view this policy in the [AWS Management Console](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/SecretsManagerReadWrite)\.
**Important**  
For security reasons, this managed policy does ***not*** include the IAM permissions needed to configure rotation\. You must explicitly grant permissions separately\. Typically, you attach the [IAMFullAccess](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/IAMFullAccess) managed policy to a Secrets Manager administrator to enable them to configure rotation\.

The Secrets Manager team maintains the AWS managed policy which can be an important advantage of using an AWS managed policy\. If the rotation process expands to include new functionality that requires additional permissions, Secrets Manager adds those permissions to the managed policy at that time\. Your rotation functions automatically receive the new permissions so that they continue to operate as expected without any interruption\.

#### Specifying Policy Statement Elements<a name="auth-and-access_policy-elements"></a>

The following section contains a brief overview of IAM permission policies from the perspective of Secrets Manager\. For more detail about IAM policy syntax, see the [AWS IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

Secrets Manager defines a set of API operations to interact with or manipulate a secret in some way\. To grant permissions for these operations, Secrets Manager defines a set of corresponding [actions](reference_iam-permissions.md) you can specify in a policy\. For example, Secrets Manager defines actions to work on a secret, such as `CreateSecret`, `GetSecretValue`, `ListSecrets`, and `RotateSecret`\.

A policy document must have a `Version` element\. We recommend always using the latest version to ensure you can use all of the available features\. As of this writing, the only available version is `2012-10-17` \(the latest version\)\. 

In addition, a secret policy document must have one `Statement` element with one or more statements in an array\. Each statement can consist of up to six elements:
+ **Sid** – \(Optional\) You can use the `Sid` as a statement identifier, an arbitrary string identifying the statement\. The string cannot contain spaces\.
+ **Effect** – \(Required\) Use this keyword to specify if the policy statement allows or denies the action on the resource\. If you don't explicitly allow access to a resource, access is *implicitly* denied\. You also can explicitly deny access to a resource\. You might do this to ensure that a user can't perform the specified action on the specified resource, even if a different policy grants access\. You should understand that multiple statements overlap, explicit deny in a statement overrides any other statements that explicitly allow\. Explicit allow statements override the implicit deny present by default\.
+ **Action** – \(Required\) Use this keyword to identify the actions you want to allow or deny\. These actions usually, but not always, correspond one\-to\-one with the available operations\. For example, depending on the specified `Effect`, `secretsmanager:PutSecretValue` either allows or denies the user permissions to perform the Secrets Manager `PutSecretValue` operation\.
+ **Resource** – \(Required\) In an identity\-based policy attached to a user, group, or role, you use this keyword to specify the Amazon Resource Name \(ARN\) of the resource for the applicable policy statement\. If you don't want the statement to restrict access to a specific resource, then you can use "\*", and the resulting statement restricts only actions\. In a resource\-based policy attached to a secret, the resource must *always* be "\*"\.
+ **Principal** – \(Required in a resource\-based policy only\) For resource\-based policies attached directly to a secret, you specify the user, role, account, service, or other entity you want to receive permissions\. This element isn't valid in an identity\-based policy\. In identity\-based policies, the user or role with the attached policy automatically and implicitly becomes the principal\.
+ **Condition** – \(Optional\) Use this keyword to specify additional conditions that must be true for the statement to "match" and the `Effect` to apply\. For more information, see [IAM JSON Policy Elements: Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html)\.

#### Identity\-based Policies<a name="auth-and-access_iam-policies"></a>

You can attach policies to IAM identities\. For example, you can do the following:
+ **Attach a permissions policy to a user or a group in your account** – To grant a user permissions to create a secret, you can attach a permissions policy directly to the user or to a group the user belongs to \(recommended\)\.
+ **Attach a permissions policy to a role** – You can attach a permissions policy to an IAM role to grant access to a secret to anyone assuming the role\. This includes users in the following scenarios:
  + **Federated users** – You can grant permissions to users authenticated by an identity system other than IAM\. For example, you can associate IAM roles to mobile app users signing in using Amazon Cognito\. The role grants the app temporary credentials with the permissions in the role permission policy\. Those permissions can include access to a secret\. For more information, see [What is Amazon Cognito?](https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html) in the *Amazon Cognito Developer Guide*\. 
  + **Applications running on EC2 instances** – You can grant permissions to applications running on an Amazon EC2 instance by attaching an IAM role to the instance\. When an application in the instance invokes an AWS API, the application can get AWS temporary credentials from the instance metadata\. These temporary credentials associate with a role, and limited by the role permissions policy\. Those permissions can include access to a secret\.
  + **Cross\-account access** – An administrator in Account A can create a role to grant permissions to a user in a different Account B\. For example:

    1. The Account A administrator creates an IAM role and attaches a permissions policy to the role\. The policy grants access to secrets in account A and specifies the actions users perform on the secrets\.

    1. The Account A administrator attaches a trust policy to the role that identifies the account ID of Account B in the `Principal` element to specify who assumes the role\.

    1. The Account B administrator can then delegate permissions to any users in account B to enable them to assume account A role\. Doing this allows users in account B to access the secrets in the first account\. 

For more information about using IAM to delegate permissions, see [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) in the *IAM User Guide*\.

The following example policy can be attached to a user, group, or role\. The policy allows the affected user or role to perform the `DescribeSecret` and `GetSecretValue` operations in your account only on secrets with a name beginning with the path "TestEnv/"\. The policy also restricts the user or role to retrieving only the version of the secret with the staging label `AWSCURRENT` attached\. Replace the *<region>* and *<account\_id>* placeholders in the following examples with your actual values\.

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
            "Resource": "arn:aws:secretsmanager:<region>:<account_id>:secret:TestEnv/*",
            "Condition" : { 
                "ForAnyValue:StringLike" : {
                    "secretsmanager:VersionStage" : "AWSCURRENT" 
                } 
            }
        }
    ]
}
```

Because a secret version can have multiple staging labels attached, you need to use the [IAM policy language's "set operators"](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_multi-value-conditions.html) to compare them in a policy\. In the previous example, `ForAnyValue:StringLike` states if any one of the staging labels attached to the secret version under evaluation matches the string "`AWSCURRENT`", then the statement matches, and applies the string `Effect`\.

For more example identity\-based policies, see [Using Identity\-based Policies \(IAM Policies\) for Secrets Manager](auth-and-access_identity-based-policies.md)\. For more information about users, groups, roles, and permissions, see [Identities \(Users, Groups, and Roles\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html) in the *IAM User Guide*\.

#### Resource\-based Policies<a name="auth-and-access_resource-policies"></a>

Each Secrets Manager secret can have one resource\-based permissions policy,a *secret policy*, attached to it\. You can use a secret policy to grant cross\-account permissions as an alternative to using identity\-based policies with IAM roles\. For example, you can grant permissions to a user in Account B to access your secret in Account A by adding permissions to the secret policy, and identifying the user in Account B as a `Principal`, instead of creating an IAM role\.

The following example describes a Secrets Manager secret policy with one statement\. The statement allows the administrator of account 123456789012 to delegate permissions to users and roles in that account\. The policy limits the permissions the administrator can delegate to the `secretsmanager:GetSecretValue` action on a single secret named "prod/ServerA\-a1b2c3"\. The condition ensures the user can retrieve only the current version of the secret\.

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
When an IAM principal from one account accesses a secret in a different account, the secret must be encrypted by using a custom AWS KMS CMK\. A secret encrypted with the default Secrets Manager CMK for the account can be decrypted only by principals in that account\. A principal from a different account must be granted permission to both the secret and to the custom AWS KMS CMK\.   
As an alternative, you can grant the user cross\-account access to assume an IAM role in the same account as the secret\. Because the role exists in the same account as the secret, the role can access secrets encrypted with the default AWS KMS key for the account\.

Because a secret version can have multiple staging labels attached, you need to use the [IAM policy language's set operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_multi-value-conditions.html) to compare them in a policy\. In the previous example, `ForAnyValue:StringLike` states that if any one of the labels attached to the version under evaluation matches "`AWSCURRENT`", then the statement matches, and applies the `Effect`\.

For more example resource\-based policies with Secrets Manager, see [Using Resource\-based Policies for Secrets Manager](auth-and-access_resource-based-policies.md)\. For additional information about using resource\-based policies instead of IAM roles \(identity\-based policies\), see [How IAM Roles Differ from Resource\-based Policies](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_compare-resource-policies.html) in the *IAM User Guide*\.