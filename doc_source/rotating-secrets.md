# Rotating your AWS Secrets Manager secrets<a name="rotating-secrets"></a>

To help keep your secrets secure, Secrets Manager can automatically rotate them on a schedule\. When it rotates a secret, Secrets Manager updates the credentials in both the secret and the database or service so that you don't have to manually change the credentials\. Secrets Manager uses a Lambda rotation function to communicate with both Secrets Manager and the database or service\. The rotation function:
+ Calls the Secrets Manager API to retrieve and update secrets\.
+ Sends requests to the database or service to update the user password\.



For Amazon RDS, Amazon DocumentDB, and Amazon Redshift secrets, you can turn on automatic rotation\. For more information, see [Rotating secrets for databases with built\-in rotation support](rotating-secrets-built-in.md)\.

For other types of secrets, because each service or database can have a unique way of configuring secrets, you write a Lambda function to implement the service\-specific details of rotating a secret\. For more information, see [Enabling rotation for a secret for another database or service](enable-rotation-other.md)\. 

## VPC<a name="rotation-network-rqmts"></a>

If your service runs in a VPC and is publicly accessible, then the Lambda rotation function communicates with it through the publicly accessible connection point\. 

If your service is not publicly accessible, you can configure your Lambda rotation function to run in the same VPC\. so that the rotation function can communicate with the service\. For more information, see [Configuring VPC access](https://docs.aws.amazon.com/lambda/latest/dg/configuration-vpc.html#vpc-configuring)\.

If your Lambda function runs in a VPC, then to allow it to communicate with Secrets Manager, you have two options:
+ You can enable your Lambda function to access the public Secrets Manager endpoint by adding a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) to your VPC, which allows traffic from your VPC to reach the public endpoint\. This exposes your VPC to a level of risk because an IP address for the gateway can be attacked from the public Internet\.
+ You can configure Secrets Manager service endpoints within your VPC\. This allows your VPC to intercept any request addressed to the public regional endpoint and redirect the VPC to the private service endpoint running within your VPC\. For more information, see [VPC endpoints](vpc-endpoint-overview.md)\.

## Permissions required to automatically rotate secrets<a name="rotating-secrets-required-permissions"></a>

When you use the AWS Secrets Manager console to configure rotation for a secret for one of the [fully supported databases](intro.md#rds-supported-database-list), the console configures almost all parameters for you\. But if you create a function or opt to do anything manually for other reasons, you also might have to manually configure the permissions for that part of the rotation\.

### Permissions for users configuring rotation compared to users triggering rotation<a name="rotating-secrets-required-permissions-user-vs-function"></a>

Secrets Manager requires two separate sets of permissions for ***user*** operations using secret rotation:
+ **Permissions required to configure rotation** – Secrets Manager assigns permissions to a trusted user to configure secret rotation\. For more information, see [Granting Full Secrets Manager Administrator Permissions to a User](https://docs.aws.amazon.com/secretsmanager/latest/userguide/auth-and-access_identity-based-policies.html#permissions_grant-admin-actions)\.
+ **Permissions required to rotate the secret** – An IAM user only needs the permission, `secretsmanager:RotateSecret`, to trigger rotation after configuration\. After rotation starts, the Lambda rotation function takes over and uses the attached IAM role and the permissions to authorize the operations performed during rotation, including any required AWS KMS operations\.

The rest of this topic discusses the permissions for the Lambda rotation function to successfully rotate a secret\.

### Permissions associated with the Lambda rotation function<a name="rotating-secrets-required-permissions-function"></a>

AWS Secrets Manager uses a Lambda function to implement the code to rotate the credentials in a secret\.

Secrets Manager service invokes the Lambda function\. The service does this by invoking an IAM role attached to the Lambda function\. Secrets Manager has two actions for this: 
+ **The *trust policy* specifying who can assume the role**\. You must configure this policy to allow Secrets Manager to assume the role, as identified by the service principal: `secretsmanager.amazonaws.com`\. You can view this policy in the Lambda console by selecting a secret Lambda function from the list and then choosing the **Permissions**tab\. In the **Resource\-based policy** section, you should see code similar to the following example:

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