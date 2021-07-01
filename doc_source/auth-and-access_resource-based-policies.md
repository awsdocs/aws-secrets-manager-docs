# Using resource\-based policies for Secrets Manager<a name="auth-and-access_resource-based-policies"></a>

You control access to secrets in AWS Secrets Manager using secret resource\-based policies\. AWS defines a *secret* as a *resource* in Secrets Manager\. You, as the account administrator, control access to a resource in an AWS service\. You can add permissions to the policy attached to a secret\. Secrets Manager refers to permissions policies attached directly to secrets as *resource\-based policies*\. You can use resource\-based policies to manage secret access and management permissions\.

A resource\-based policy has an advantage over identity\-based policies because a resource\-based policy enables you to grant access to principals from different accounts\. See the second example in the following section\.

For an overview of the basic elements for policies, see [Managing access to resources with policies](auth-and-access_resource-access.md)\.

For information about how to create a resource\-based policy, see [Attaching a resource\-based policy to a secret](manage_secret-policy.md#manage_secret-policy_attach)\.

For information about alternative, identity\-based permission policies, see [Using identity\-based policies \(IAM policies\) and ABAC for Secrets Manager](auth-and-access_identity-based-policies.md)\.

**Topics**
+ [Controlling access to secrets for principals](permissions_grant-limited-principal.md)
+ [Granting read\-only access to a role](#example_1)

## Granting read\-only access to a role<a name="example_1"></a>

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