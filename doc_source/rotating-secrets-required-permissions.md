# Permissions Required to Automatically Rotate Secrets<a name="rotating-secrets-required-permissions"></a>

When you use the AWS Secrets Manager console to configure rotation for a secret for one of the [fully supported databases](intro.md#rds-supported-database-list), the console configures almost all parameters for you\. But if you create a function or opt to do anything manually for other reasons, you also might have to manually configure the permissions for that part of the rotation\.

## Permissions for Users Configuring Rotation vs\. Users Triggering Rotation<a name="rotating-secrets-required-permissions-user-vs-function"></a>

Secrets Manager requires two separate sets of permissions for ***user*** operations using secret rotation:
+ **Permissions required to configure rotation** – Secrets Manager assigns permissions to a trusted user to configure secret rotation\. For more information, see [Granting Full Secrets Manager Administrator Permissions to a User](https://docs.aws.amazon.com/secretsmanager/latest/userguide/auth-and-access_identity-based-policies.html#permissions_grant-admin-actions)\.
+ **Permissions required to rotate the secret** – An IAM user only needs the permission, `secretsmanager:RotateSecret`, to trigger rotation after configuration\. After rotation starts, the Lambda rotation function takes over and uses the attached IAM role and the permissions to authorize the operations performed during rotation, including any required AWS KMS operations\.

The rest of this topic discusses the permissions for the Lambda rotation function to successfully rotate a secret\.

## Permissions Associated with the Lambda Rotation Function<a name="rotating-secrets-required-permissions-function"></a>

AWS Secrets Manager uses a Lambda function to implement the code to rotate the credentials in a secret\.

Secrets Manager service invokes the the Lambda function\. The service does this by invoking an IAM role attached to the Lambda function\. Secrets Manager has two actions for this: 
+ **The *trust policy* specifying who can assume the role**\. You must configure this policy to allow Secrets Manager to assume the role, as identified by the service principal: `secretsmanager.amazonaws.com`\. You can view this policy in the Lambda console on the function details page by clicking the key icon in the **Designer** section\. The details appear in the **Function policy** section\. This policy should look similar to the following example:

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

  For security reasons, the trust policy created by Secrets Manager includes a `Resource` element and includes the Amazon Resource Name \(ARN\) of the Lambda function\. Anyone who assumes the role can invoke *only* the Lambda function associated with the role\.
+ **The *permissions policy* for the role specifying the role of the assumer ** You must configure this policy to specify the permissions Secrets Manager can use when assuming the role by invoking the function\. Secrets Manager has two different policies available, depending on the rotation strategy to implement\.
  + **Single user rotation**: The following example describes a function that rotates a secret by signing in with the credentials stored in the secret, and changing the password\.

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
                    "ec2:DescribeNetworkInterfaces"
                ],
                "Resource": "*",
                "Effect": "Allow"
            }
        ]
    }
    ```

    The first statement in the single\-user example grants permission for the function to run Secrets Manager operations\. However, the `Condition` element restricts this to only secrets configured with this Lambda function ARN as the secret rotation Lambda function\.

    The second statement allows one additional Secrets Manager operation that doesn't require the condition\.

    The third statement enables Lambda to set up the required configuration when you specify running your database or service in a VPC\. For more information, see [Configuring a Lambda Function to Access Resources in an Amazon VPC](https://docs.aws.amazon.com/lambda/latest/dg/vpc.html) in the *AWS Lambda Developer Guide*\.
  + **Master user rotation**: The following example describes a function that rotates a secret by signing in using a separate ** master secret** with credentials containing elevated permissions\. If you use one of the rotation strategies alternate between two users, Secrets Manager requires this function\.

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
                    "ec2:DescribeNetworkInterfaces"
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

    In addition to the same three statements as the previous single\-user policy, this policy adds a fourth statement\. The fourth statement enables the function to retrieve the credentials in the master secret\. Secrets Manager uses the credentials in the master secret to sign in to the secured database and update the credentials in the rotated secret\.