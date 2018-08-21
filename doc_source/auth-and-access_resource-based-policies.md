# Using Resource\-based Policies for Secrets Manager<a name="auth-and-access_resource-based-policies"></a>

One way that you can control access to secrets in AWS Secrets Manager is to use secret \(resource\-based\) policies\. A *secret* is defined as a *resource* in Secrets Manager\. A resource in an AWS service is something that you, the account administrator, can control access to\. You can add permissions to the policy that's attached to a secret\. Permissions policies that are attached directly to secrets are referred to as *resource\-based policies*\. You can use resource\-based policies to manage secret access and management permissions\.

One advantage of resource\-based policies over identity\-based policies is that a resource\-based policy enables you to grant access to principals from different accounts\. See the second example in the first section that follows\.

For an overview of the basic elements for policies, see [Managing Access to Resources with Policies](auth-and-access_overview.md#auth-and-access_resource-access)\.

For information about the alternative, identity\-based permission policies, see [Using Identity\-based Policies \(IAM Policies\) for Secrets Manager](auth-and-access_identity-based-policies.md)\.

**Topics**
+ [Controlling Which Principals Can Access a Secret](#permissions_grant-limited-principal)
+ [Grant Read\-Only Access to a Role](#example_1)

## Controlling Which Principals Can Access a Secret<a name="permissions_grant-limited-principal"></a>

When you use a resource\-based policy with Secrets Manager that's attached directly to a secret, the `Resource` automatically and implicitly becomes the secret that the policy is attached to\. You now get to specify the `Principal` element\. The `Principal` element enables you to specify which IAM users, groups, roles, accounts, or AWS service principals can access this secret, and which actions they can perform on this secret\. For more information about all the different ways to specify a `Principal`, see [Principal](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Principal) in the *IAM User Guide*\.

**Example: Granting permission to a user in the same account as the secret**  
The following policy, when it's attached directly to a secret \(as part of the metadata\), grants the user Anaya permission to run any Secrets Manager operation on it\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "secretsmanager:*",
            "Principal": {"AWS": "arn:aws:iam::123456789012:user/anaya"},
            "Resource": "*"
        }
    ]
}
```

[Suggest improvements to this example on GitHub\.](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/iam_policies/secretsmanager/asm-resource-policy-grant-all-perms-to-anaya.json)

**Example: Granting permission to authorized users in a different account**  
The following policy, when it's attached directly to a secret \(as part of the metadata\), grants the IAM user Mateo in the account 123456789012 access to read any version of a specific secret:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {"AWS": arn:aws:iam::123456789012:user/mateo" },
            "Action": "secretsmanager:GetSecretValue",
            "Resource": "*"
        }
    ]
}
```

[Suggest improvements to this example on GitHub\.](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/iam_policies/secretsmanager/asm-resource-policy-grant-only-gsv-to-mateo.json)

## Grant Read\-Only Access to a Role<a name="example_1"></a>

A common Secrets Manager scenario is where an application that's running on an Amazon EC2 instance needs access to a database to perform its required tasks\. The application must retrieve the database credentials from Secrets Manager\. To make a request to Secrets Manager, like any other AWS service, you must have AWS credentials with permissions to perform the request\. The recommended way to achieve this is to create an IAM role that's attached to the EC2 instance profile\. For more information, see [IAM Roles for Amazon EC2]() in the *Amazon EC2 User Guide for Linux Instances*—and specifically the section [Retrieving Security Credentials from Instance Metadata](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/auth-and-access.xmliam-roles-for-amazon-ec2.html#instance-metadata-security-credentials)\.

If you then attach the following example resource\-based policy to the secret, any requests to retrieve the secret work only if the requester is using credentials that are associated with that role, and if the request asks only for the current version of the secret:

```
{
  "Version" : "2012-10-17",
  "Statement" : [
    {
      "Effect": "Allow",
      "Principal": {"AWS": "arn:aws:iam::123456789012:role/EC2RoleToAccessSecrets"},
      "Action": "secretsmanager:GetSecretValue",
      "Resource": "*",
      "Condition": {
        "ForAnyValue:StringEquals": {
          "secretsmanager:VersionStage" : "AWSCURRENT"
        }
      }
    }
  ]
}
```

[Suggest improvements to this example on GitHub\.](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/iam_policies/secretsmanager/asm-resource-policy-grant-gsv-on-only-awscurrent-to-role.json.json)