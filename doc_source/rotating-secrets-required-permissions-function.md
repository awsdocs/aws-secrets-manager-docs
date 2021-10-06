# Permissions for the Lambda rotation function<a name="rotating-secrets-required-permissions-function"></a>

Secrets Manager uses a Lambda function to rotate a secret\. The Lambda function has a resource policy that allows Secrets Manager to invoke it\.

Secrets Manager calls the Lambda function by invoking an [IAM execution role](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html) attached to the Lambda function\. Permissions for the Lambda function are granted through the IAM execution role as inline policies\.
+ For an [Amazon RDS, Amazon DocumentDB, or Amazon Redshift secret](rotate-secrets_turn-on-for-db.md), Secrets Manager creates the role for you and attaches permissions to it\. 
+ For an [Other type of secret](rotate-secrets_turn-on-for-other.md), if you create the Lambda function through the Secrets Manager console or the AWS Serverless Application Repository console, the role is created for you\. If you create the Lambda function another way, you need to also create an execution role and make sure it has the correct permissons\.

For information about permissions to access secrets, see [Permissions to access secrets](auth-and-access.md#auth-and-access_secrets)\.

**Example Lambda function resource policy**  
The following policy allows Secrets Manager to assume the role by identifying the service as the `Principal`: `secretsmanager.amazonaws.com`, and it allows Secrets Manager to invoke the Lambda function in the `Resource`\.  

```
{
  "Version": "2012-10-17",
  "Id": "default",
  "Statement": [
    {
      "Sid": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE",
      "Effect": "Allow",
      "Principal": {
        "Service": "secretsmanager.amazonaws.com"
      },
      "Action": "lambda:InvokeFunction",
      "Resource": "LambdaRotationFunctionARN"
    }
  ]
}
```

**Example IAM execution role inline policy for single user rotation strategy**  
For an [Automatically rotate an Amazon RDS, Amazon DocumentDB, or Amazon Redshift secret](rotate-secrets_turn-on-for-db.md), Secrets Manager creates the IAM execution role and attaches this policy for you\.   
The following example policy does the following:  
+ Allows the rotation function to run Secrets Manager operations for secrets that are configured to use this rotation function\.
+ Allows the function to create a new password\.
+ Allows Lambda to set up the required configuration if you your database or service runs in a VPC\. For more information, see [Configuring a Lambda function to access resources in a VPC](https://docs.aws.amazon.com/lambda/latest/dg/vpc.html)\.

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
            "Resource": "SecretARN",
            "Condition": {
                "StringEquals": {
                    "secretsmanager:resource/AllowRotationLambdaArn": "LambdaRotationFunctionARN"
                }
            }
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

**Example IAM execution role inline policy statement for alternating users strategy**  
For an [Automatically rotate an Amazon RDS, Amazon DocumentDB, or Amazon Redshift secret](rotate-secrets_turn-on-for-db.md), Secrets Manager creates the IAM execution role and attaches this policy for you\.   
For the alternating users strategy, the following example shows a statement to add to the execution role policy\. This statement allows the function to retrieve the credentials in the separate secret\. Secrets Manager uses the credentials in the separate secret to update the credentials in the rotated secret\.  

```
        {
            "Effect": "Allow",
            "Action": [
                "secretsmanager:GetSecretValue"
            ],
            "Resource": "SeparateSecretARN"
        }
```

**Example IAM execution role inline policy statement for customer managed key**  
If you use a KMS key other than the AWS managed key `aws/secretsmanager` to encrypt your secret, then you need to grant the Lambda execution role permission to use the key\.   
The following example shows a statement to add to the execution role policy to allow the function to retrieve the KMS key\.  

```
        {
            "Effect": "Allow",
            "Action": [
                "kms:Decrypt",
                "kms:GenerateDataKey"
            ],
            "Resource": "*"
        }
```