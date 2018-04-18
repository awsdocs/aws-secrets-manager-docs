# Permissions Required to Automatically Rotate RDS Database Secrets<a name="rotating-secrets-required-permissions"></a>

When you use the AWS Secrets Manager console to configure rotation for a secret, the console configures just about everything for you\. So you shouldn't have to often manually configure the permissions described in this section\. But if you do anything manually, you may have to also manually configure the permissions for that part of the rotation\.

## Permissions On the Lambda Rotation Function<a name="rotating-secrets-required-permissions-function"></a>

AWS Secrets Manager uses a Lambda function to implement the code that actually rotates the credentials in a secret\.

The Lambda function is invoked by the Secrets Manager service itself\. The service does this by invoking an IAM role that is attached to the Lambda function\. There are two pieces to this: 
+ **The *trust policy* for the role that specifies who can assume the role**\. You must configure this policy to allow Secrets Manager, as identified by its service principal: `secretsmanager.amazonaws.com`\. This policy is viewable in the Lambda console on the function details page by clicking the key icon in the **Designer** section\. It then appears in the **Function policy** section\. This policy should look like the following example:

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
        "Resource": "<arn of the Lambda function that this trust policy is attached to - must match exactly>"
      }
    ]
  }
  ```
+ **The *permissions policy* for the role that specifies what the assumer of the role can do\.** You must configure this policy to allow AWS Secrets Manager, when it assumes the role by invoking the function\. There are two different policies, depending on the rotation strategy you want to implement\.
  + **Single user rotation**: The following example is suitable for a function that rotates a secret by signing in with the credentials stored in that secret and changing its own password\.

    ```
    {
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                    "secretsmanager:DescribeSecret",
                    "secretsmanager:GetSecretValue",
                    "secretsmanager:PutSecretValue",
                    "secretsmanager:UpdateSecretVersionStage"
                ],
                "Resource": "*",
                "Condition": {
                    "StringEquals": {
                        "secretsmanager:resource/AllowRotationLambdaArn": "<lambda_arn>"
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

    The first statement in the single user example grants permission to the function to run Secrets Manager operations, but only if the secret they are performed on is configured with this Lambda function's ARN as the secret's rotation Lambda function\.

    The second statement allows one additional Secrets Manager operation that does not require the condition\.

    The third statement enables the required configuration if the Lambda and the secured database and service are both running in a VPC\.
  + **Master user rotation**: The following example is suitable for a function that rotates a secret by signing in using a separate "master" secret that contains credentials with elevated permissions\. This is typically required when you use one of the rotation strategies that alternate between two users\.

    ```
    {
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                    "secretsmanager:DescribeSecret",
                    "secretsmanager:GetSecretValue",
                    "secretsmanager:PutSecretValue",
                    "secretsmanager:UpdateSecretVersionStage"
                ],
                "Resource": "*",
                "Condition": {
                    "StringEquals": {
                        "secretsmanager:resource/AllowRotationLambdaArn": "<lambda_arn>"
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
            },
            {
                "Effect": "Allow",
                "Action": [
                    "secretsmanager:GetSecretValue"
                ],
                "Resource": "<master_arn>"
            }
        ]
    }
    ```

    In addition to the same three statements as the previous single user policy, this policy adds a fourth statement that enables the function to retrieve the credentials in the master secret\. They are used to sign in to the secured database to rotate the credentials in the secret that is being rotated\.