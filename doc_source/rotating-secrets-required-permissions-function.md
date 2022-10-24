# Lambda rotation function execution role permissions for AWS Secrets Manager<a name="rotating-secrets-required-permissions-function"></a>

Secrets Manager uses a Lambda function to rotate a secret\. For the Lambda function to run, Lambda assumes an [IAM execution role](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html) and provides those credentials to the Lambda function code\. For instructions on how to set up automatic rotation, see: 
+ [Automatic rotation for database secrets \(console\)](rotate-secrets_turn-on-for-db.md)
+ [Automatic rotation \(console\)](rotate-secrets_turn-on-for-other.md)
+ [Automatic rotation \(AWS CLI\)](rotate-secrets-cli.md)

The following examples show inline policies for Lambda rotation function execution roles\. To create an execution role and attach a permissions policy, see [AWS Lambda execution role](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html)\.

**Topics**
+ [Policy for a Lambda rotation function execution role](#rotating-secrets-required-permissions-function-example)
+ [Policy statement for customer managed key](#rotating-secrets-required-permissions-function-cust-key-example)
+ [Policy statement for alternating users strategy](#rotating-secrets-required-permissions-function-alternating-example)

## Policy for a Lambda rotation function execution role<a name="rotating-secrets-required-permissions-function-example"></a>

The following example policy allows the rotation function to:
+ Run Secrets Manager operations for *SecretARN*\.
+ Create a new password\.
+ Set up the required configuration if your database or service runs in a VPC\. See [Configuring a Lambda function to access resources in a VPC](https://docs.aws.amazon.com/lambda/latest/dg/configuration-vpc.html)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "secretsmanager:DescribeSecret",
                "secretsmanager:GetSecretValue",
                "secretsmanager:PutSecretValue",
                "secretsmanager:UpdateSecretVersionStage"
            ],
            "Resource": "SecretARN"
        },
        {
            "Effect": "Allow",
            "Action": [
                "secretsmanager:GetRandomPassword"
            ],
            "Resource": "*"
        },
        {
            "Action": [
                "ec2:CreateNetworkInterface",
                "ec2:DeleteNetworkInterface",
                "ec2:DescribeNetworkInterfaces",
                "ec2:DetachNetworkInterface"
            ],
            "Resource": "*",
            "Effect": "Allow"
        }
    ]
}
```

## Policy statement for customer managed key<a name="rotating-secrets-required-permissions-function-cust-key-example"></a>

If the secret is encrypted with a KMS key other than the AWS managed key `aws/secretsmanager`, then you need to grant the Lambda execution role permission to use the key\. The following example shows a statement to add to the execution role policy to allow the function to retrieve the KMS key\.

```
        {
            "Effect": "Allow",
            "Action": [
                "kms:Decrypt",
                "kms:GenerateDataKey"
            ],
            "Resource": "KMSKeyARN"
        }
```

## Policy statement for alternating users strategy<a name="rotating-secrets-required-permissions-function-alternating-example"></a>

For information about the *alternating users rotation strategy*, see [Rotation strategy](getting-started.md#rotation-strategy)\.

The following example policy allows the function to:
+ Run Secrets Manager operations for *SecretARN*\.
+ Retrieve the credentials in the superuser secret\. Secrets Manager uses the credentials in the superuser secret to update the credentials in the rotated secret\.
+ Create a new password\.
+ Set up the required configuration if your database or service runs in a VPC\. For more information, see [Configuring a Lambda function to access resources in a VPC](https://docs.aws.amazon.com/lambda/latest/dg/vpc.html)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "secretsmanager:DescribeSecret",
                "secretsmanager:GetSecretValue",
                "secretsmanager:PutSecretValue",
                "secretsmanager:UpdateSecretVersionStage"
            ],
            "Resource": "SecretARN"
        },
        {
            "Effect": "Allow",
            "Action": [
                "secretsmanager:GetSecretValue"
            ],
            "Resource": "SeparateSecretARN"
        },
        {
            "Effect": "Allow",
            "Action": [
                "secretsmanager:GetRandomPassword"
            ],
            "Resource": "*"
        },
        {
            "Action": [
                "ec2:CreateNetworkInterface",
                "ec2:DeleteNetworkInterface",
                "ec2:DescribeNetworkInterfaces",
                "ec2:DetachNetworkInterface"
            ],
            "Resource": "*",
            "Effect": "Allow"
        }
    ]
}
```