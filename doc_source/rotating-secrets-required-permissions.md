# Permissions Required to Automatically Rotate Secrets<a name="rotating-secrets-required-permissions"></a>

When you use the AWS Secrets Manager console to configure rotation for a secret for one of the [fully supported databases](rotating-secrets-rds.md#rds-supported-database-list), the console configures just about everything for you\. So you shouldn't have to manually configure the permissions described in this section\. But if you create your own rotation function or choose to do anything manually for other reasons, you might have to also manually configure the permissions for that part of the rotation\.

## Permissions Associated with the Lambda Rotation Function<a name="rotating-secrets-required-permissions-function"></a>

AWS Secrets Manager uses a Lambda function to implement the code that actually rotates the credentials in a secret\.

The Lambda function is invoked by the Secrets Manager service itself\. The service does this by invoking an IAM role that's attached to the Lambda function\. There are two pieces to this: 
+ **The *trust policy* that specifies who can assume the role**\. You must configure this policy to allow Secrets Manager to assume the role, as identified by its service principal: `secretsmanager.amazonaws.com`\. This policy is viewable in the Lambda console on the function details page by clicking the key icon in the **Designer** section\. It then appears in the **Function policy** section\. This policy should look similar to the following example:

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

  For security reasons, the trust policy created by Secrets Manager includes a `Resource` element that includes the Amazon Resource Name \(ARN\) of the Lambda function\. That results in anyone who assumes the role being able to invoke *only* the Lambda function that's associated with the role\.
+ **The *permissions policy* for the role that specifies what the assumer of the role can do\.** You must configure this policy to specify the permissions that Secrets Manager can use when it assumes the role by invoking the function\. There are two different policies, depending on the rotation strategy you want to implement\.
  + **Single user rotation**: The following example is suitable for a function that rotates a secret by signing in with the credentials that are stored in that secret, and changing its own password\.

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

    The first statement in the single\-user example grants permission to the function to run Secrets Manager operations\. However, the `Condition` element restricts this to only secrets that are configured with this Lambda function's ARN as the secret's rotation Lambda function\.

    The second statement allows one additional Secrets Manager operation that doesn't require the condition\.

    The third statement enables Lambda to set up the required configuration when you specify that your database or service is running in a VPC\. For more information, see [Configuring a Lambda Function to Access Resources in an Amazon VPC](http://docs.aws.amazon.com/lambda/latest/dg/vpc.html) in the *AWS Lambda Developer Guide*\.
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

    In addition to the same three statements as the previous single\-user policy, this policy adds a fourth statement\. The fourth statement enables the function to retrieve the credentials in the master secret\. The credentials in the master secret are used to sign in to the secured database to update the credentials in the secret that's being rotated\.