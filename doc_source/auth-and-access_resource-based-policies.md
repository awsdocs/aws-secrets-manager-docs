# Using Resource\-based Policies for Secrets Manager<a name="auth-and-access_resource-based-policies"></a>

You control access to secrets in AWS Secrets Manager using secret resource\-based policies\. AWS defines a *secret* as a *resource* in Secrets Manager\. You, as the account administrator, control access to a resource in an AWS service\. You can add permissions to the policy attached to a secret\. Secrets Manager refers to permissions policies attached directly to secrets as *resource\-based policies*\. You can use resource\-based policies to manage secret access and management permissions\.

A resource\-based policy has an advantage over identity\-based policies because a resource\-based policy enables you to grant access to principals from different accounts\. See the second example in the following section\.

For an overview of the basic elements for policies, see [Managing Access to Resources with Policies](auth-and-access_overview.md#auth-and-access_resource-access)\.

For information about alternative, identity\-based permission policies, see [Using Identity\-based Policies \(IAM Policies\) for Secrets Manager](auth-and-access_identity-based-policies.md)\.

**Topics**
+ [Controlling Access to Secrets for Principals](#permissions_grant-limited-principal)
+ [Granting Read\-Only Access to a Role](#example_1)

## Controlling Access to Secrets for Principals<a name="permissions_grant-limited-principal"></a>

When you use a resource\-based policy with Secrets Manager attached directly to a secret, the `Resource` automatically and implicitly becomes the secret with the attached policy\. You can now specify the `Principal` element\. The `Principal` element enables you to specify the IAM users, groups, roles, accounts, or AWS service principals that can access this secret, and the actions they can perform on this secret\. For more information about all the different ways to specify a `Principal`, see [Principal](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Principal) in the *IAM User Guide*\.

**Example: Granting permission to a user in the same account as the secret**  
The following policy, when attached directly to a secret as part of the metadata, grants the user Anaya permission to run any Secrets Manager operation on the secret\.\.

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

**Example: Granting permission to authorized users in a different account**  
The following policy, when attached directly to a secret as part of the metadata, grants the IAM user Mateo in the account 123456789012 access to read any version of a specific secret:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {"AWS": "arn:aws:iam::123456789012:user/mateo" },
            "Action": "secretsmanager:GetSecretValue",
            "Resource": "*"
        }
    ]
}
```

## Granting Read\-Only Access to a Role<a name="example_1"></a>

A common Secrets Manager scenario may be an application running on an Amazon EC2 instance and needs access to a database to perform required tasks\. The application must retrieve the database credentials from Secrets Manager\. To send a request to Secrets Manager, like any other AWS service, you must have AWS credentials with permissions to perform the request\. You can achieve this by creating an IAM role attached to the EC2 instance profile\. For more information, see [IAM Roles for Amazon EC2]() in the Amazon EC2 User Guide for Linux Instances and specifically the section [Retrieving Security Credentials from Instance Metadata](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html#instance-metadata-security-credentials)\.

If you then attach the following example resource\-based policy to the secret, any requests to retrieve the secret work only if the requester uses credentials associated with the role, and if the request asks only for the current version of the secret:

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