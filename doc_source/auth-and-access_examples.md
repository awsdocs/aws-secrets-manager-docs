# Permissions policy examples<a name="auth-and-access_examples"></a>

A permissions policy is JSON structured text\. See [JSON policy document structure](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html#policies-introduction)\. 

Permissions policies that you attach to resources and identities are very similar\. Some elements you include in a policy for access to secrets include:
+ `Principal`: who to grant access to\. See [Specifying a principal](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_principal.html#Principal_specifying) in the *IAM User Guide*\. When you attach a policy to an identity, you don't include a `Principal` element in the policy\.

  You can grant permissions to an application that retrieves a secret from Secrets Manager\. For example, an application running on an Amazon EC2 instance might need access to a database\. You can create an IAM role attached to the EC2 instance profile and then use a permissions policy to grant the role access to the secret\.

  You can also grant permissions to users authenticated by an identity system other than IAM\. For example, you can associate IAM roles to mobile app users who sign in with Amazon Cognito\. The role grants the app temporary credentials with the permissions in the role permission policy\. Then you can use a permissions policy to grant the role access to the secret\. 

  [AWS service principals](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_principal.html#principal-services) are not typically used as principals in a policy attached to a secret, but some AWS services require it\. When the principal is a service principal, we recommend that you use the [aws:SourceArn](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn) and [aws:SourceAccount](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount) global condition keys\. See [Example: Service principal](#auth-and-access_service)\.
+ `Action`: what they can do\. See [Secrets Manager actions](reference_iam-permissions.md#reference_iam-permissions_actions)\.
+ `Resource`: which secrets they can access\. See [Secrets Manager resources](reference_iam-permissions.md#iam-resources)\. 

  The wildcard character \(\*\) has different meaning depending on what you attach the policy to:
  + In a policy attached to a secret, `*` means the policy applies to this secret\.
  + In a policy attached to an identity, `*` means the policy applies to all resources, including secrets, in the account\. 

To attach a policy to a secret, see [Attach a permissions policy to a secret](auth-and-access_resource-policies.md)\.

To attach a policy to an identity, see [Attach a permissions policy to an identity](auth-and-access_iam-policies.md)\.

**Topics**
+ [Example: Permission to retrieve secret values](#auth-and-access_examples_read)
+ [Example: Wildcards](#auth-and-access_examples_wildcard)
+ [Example: Permission to create secrets](#auth-and-access_examples_create)
+ [Example: Permissions and VPCs](#auth-and-access_examples_vpc)
+ [Example: Control access to secrets using tags](#tag-secrets-abac)
+ [Example: Limit access to identities with tags that match secrets' tags](#auth-and-access_tags2)
+ [Example: Service principal](#auth-and-access_service)

## Example: Permission to retrieve secret values<a name="auth-and-access_examples_read"></a>

To grant permission to retrieve secret values, you can attach policies to secrets or identities\. For help determining which type of policy to use, see [Identity\-based policies and resource\-based policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_identity-vs-resource.html)\. For information about how to attach a policy, see [Attach a permissions policy to a secret](auth-and-access_resource-policies.md) and [Attach a permissions policy to an identity](auth-and-access_iam-policies.md)\.

The following examples show two different ways to grant access to a secret\. The first example is a resource\-based policy that you can attach to a secret\. This example is useful when you want to grant access to a single secret to multiple users or roles\. The second example is an identity\-based policy that you can attach to a user or role in IAM\. This example is useful when you want to grant access to an IAM group\.

**Example Read one secret \(attach to a secret\)**  
You can grant access to a secret by attaching the following policy to the secret\. To use this policy, see [Attach a permissions policy to a secret](auth-and-access_resource-policies.md)\.  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::AccountId:role/EC2RoleToAccessSecrets"
      },
      "Action": "secretsmanager:GetSecretValue",
      "Resource": "*"
    }
  ]
}
```

**Example Read one secret \(attach to an identity\)**  
You can grant access to a secret by attaching the following policy to an identity\. To use this policy, see [Attach a permissions policy to an identity](auth-and-access_iam-policies.md)\. If you attach this policy to the role *EC2RoleToAccessSecrets*, it grants the same permissions as the previous policy\.   

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "secretsmanager:GetSecretValue",
      "Resource": "SecretARN"
    }
  ]
}
```

## Example: Wildcards<a name="auth-and-access_examples_wildcard"></a>

You can use wildcards to include a set of values in a policy element\. 

**Example Access all secrets in a path \(attach to identity\)**  
The following policy grants access to retrieve all secrets with a name beginning with "*TestEnv/*"\. To use this policy, see [Attach a permissions policy to an identity](auth-and-access_iam-policies.md)\.  

```
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": "secretsmanager:GetSecretValue",
    "Resource": "arn:aws:secretsmanager:Region:AccountId:secret:TestEnv/*"
  }
}
```

**Example Access metadata on all secrets \(attach to identity\)**  
The following policy grants `DescribeSecret` and permissions beginning with `List`: `ListSecrets` and `ListSecretVersionIds`\. To use this policy, see [Attach a permissions policy to an identity](auth-and-access_iam-policies.md)\.  

```
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": [
      "secretsmanager:DescribeSecret",
      "secretsmanager:List*"
    ],
    "Resource": "*"
  }
}
```

**Example Match secret name \(attach to identity\)**  
The following policy grants all Secrets Manager permissions for a secret by name\. To use this policy, see [Attach a permissions policy to an identity](auth-and-access_iam-policies.md)\.  
To match a secret name, you create the ARN for the secret by putting together the Region, Account ID, secret name, and the wildcard \(`?`\) to match individual random characters\. Secrets Manager appends six random characters to secret names as part of their ARN, so you can use this wildcard to match those characters\. If you use the syntax `"another_secret_name-*"`, Secrets Manager matches not only the intended secret with the 6 random characters, but also matches `"another_secret_name-<anything-here>a1b2c3"`\.   
Because you can predict all of the parts of the ARN of a secret except the 6 random characters, using the wildcard character `'??????'` syntax enables you to securely grant permissions to a secret that doesn't yet exist\. Be aware, however, if you delete the secret and recreate it with the same name, the user automatically receives permission to the new secret, even though the 6 characters changed\.   

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "secretsmanager:*",
      "Resource": [
        "arn:aws:secretsmanager:Region:AccountId:secret:a_specific_secret_name-a1b2c3",
        "arn:aws:secretsmanager:Region:AccountId:secret:another_secret_name-??????"
      ]
    }
  ]
}
```

## Example: Permission to create secrets<a name="auth-and-access_examples_create"></a>

To grant a user permissions to create a secret, we recommend you attach a permissions policy to an IAM group the user belongs to\. See [IAM user groups](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_groups.html)\.

**Example Create secrets \(attach to identity\)**  
The following policy grants permission to create secrets and view a list of secrets\. To use this policy, see [Attach a permissions policy to an identity](auth-and-access_iam-policies.md)\.  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "secretsmanager:CreateSecret",
        "secretsmanager:ListSecrets"
      ],
      "Resource": "*"
    }
  ]
}
```

## Example: Permissions and VPCs<a name="auth-and-access_examples_vpc"></a>

If you need to access Secrets Manager from within a VPC, you can make sure that requests to Secrets Manager come from the VPC by including a condition in your permissions policies\. For more information, see [VPC endpoint conditions](reference_iam-permissions.md#iam-contextkeys-vpcendpoint) and [Using AWS Secrets Manager with a VPC endpoint](vpc-endpoint-overview.md)\.

Make sure that requests to access the secret from other AWS services also come from the VPC, otherwise this policy will deny them access\.

**Example Require requests to come through a VPC endpoint \(attach to secret\)**  
The following policy allows a user to perform Secrets Manager operations only when the request comes through the VPC endpoint *`vpce-1234a5678b9012c`*\. To use this policy, see [Attach a permissions policy to a secret](auth-and-access_resource-policies.md)\.  

```
{
  "Id": "example-policy-1",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "RestrictGetSecretValueoperation",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "secretsmanager:GetSecretValue",
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:sourceVpce": "vpce-1234a5678b9012c"
        }
      }
    }
  ]
}
```

**Example Require requests to come from a VPC \(attach to secret\)**  
The following policy allows commands to create and manage secrets only when they come from *`vpc-12345678`*\. In addition, the policy allows operations that use access the secret encrypted value only when the requests come from `vpc-2b2b2b2b`\. You might use a policy like this one if you run an application in one VPC, but you use a second, isolated VPC for management functions\. To use this policy, see [Attach a permissions policy to a secret](auth-and-access_resource-policies.md)\.  

```
{
  "Id": "example-policy-2",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowAdministrativeActionsfromONLYvpc-12345678",
      "Effect": "Deny",
      "Principal": "*",
      "Action": [
        "secretsmanager:Create*",
        "secretsmanager:Put*",
        "secretsmanager:Update*",
        "secretsmanager:Delete*",
        "secretsmanager:Restore*",
        "secretsmanager:RotateSecret",
        "secretsmanager:CancelRotate*",
        "secretsmanager:TagResource",
        "secretsmanager:UntagResource"
      ],
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:sourceVpc": "vpc-12345678"
        }
      }
    },
    {
      "Sid": "AllowSecretValueAccessfromONLYvpc-2b2b2b2b",
      "Effect": "Deny",
      "Principal": "*",
      "Action": [
        "secretsmanager:GetSecretValue"
      ],
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:sourceVpc": "vpc-2b2b2b2b"
        }
      }
    }
  ]
}
```

## Example: Control access to secrets using tags<a name="tag-secrets-abac"></a>

You can use tags to control access to your secrets\. Using tags to control permissions is helpful in environments that are growing rapidly and helps with situations where policy management becomes cumbersome\. One strategy is to attach tags to secrets and then grant permissions to an identity when a secret has a specific tag\.

**Example Allow access to secrets with a specific tag \(attach to an identity\)**  
The following policy allows `DescribeSecret` on secrets with a tag with the key "*ServerName*" and the value "*ServerABC*"\. To use this policy, see [Attach a permissions policy to an identity](auth-and-access_iam-policies.md)\.  

```
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": "secretsmanager:DescribeSecret",
    "Resource": "*",
    "Condition": {
      "StringEquals": {
        "secretsmanager:ResourceTag/ServerName": "ServerABC"
      }
    }
  }
}
```

## Example: Limit access to identities with tags that match secrets' tags<a name="auth-and-access_tags2"></a>

One strategy is to attach tags to both secrets and IAM identities\. Then you create permissions policies to allow operations when the identity's tag matches the secret's tag\. For a complete tutorial, see [Define permissions to access secrets based on tags\.](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_attribute-based-access-control.html)

Using tags to control permissions is helpful in environments that are growing rapidly and helps with situations where policy management becomes cumbersome\. For more information, see [What is ABAC for AWS?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_attribute-based-access-control.html) 

**Example Allow access to roles that have the same tags as secrets \(attach to a secret\)**  
The following policy grants `GetSecretValue` to account *`123456789012`* only if the tag *`AccessProject`* has the same value for the secret and the role\. To use this policy, see [Attach a permissions policy to a secret](auth-and-access_resource-policies.md)\.  

```
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Principal": {
      "AWS": "123456789012"
    },
    "Condition": {
      "StringEquals": {
        "aws:ResourceTag/AccessProject": "${ aws:PrincipalTag/AccessProject }"
      }
    },
    "Action": "secretsmanager:GetSecretValue",
    "Resource": "*"
  }
}
```

## Example: Service principal<a name="auth-and-access_service"></a>

If the resource policy attached to your secret includes an [AWS service principal](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_principal.html#principal-services), we recommend that you use the [aws:SourceArn](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn) and [aws:SourceAccount](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount) global condition keys\. The ARN and account values are included in the authorization context only when a request comes to Secrets Manager from another AWS service\. This combination of conditions avoids a potential [confused deputy scenario](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html)\. 

Service principals are not typically used as principals in a policy attached to a secret, but some AWS services require it\. For information about resource policies that a service requires you to attach to a secret, see the service's documentation\.

**Example Allow a service to access a secret using a service principal \(attach to a secret\)**  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": [
          "service-name.amazonaws.com"
        ]
      },
      "Action": "secretsmanager:GetSecretValue",
      "Resource": "*",
      "Condition": {
        "ArnLike": {
          "aws:sourceArn": "arn:aws:service-name::123456789012:*"
        },
        "StringEquals": {
          "aws:sourceAccount": "123456789012"
        }
      }

    }
  ]
}
```