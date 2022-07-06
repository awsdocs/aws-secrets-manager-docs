# Troubleshoot AWS Secrets Manager rotation of secrets<a name="troubleshoot_rotation"></a>

Use the information here to help you diagnose and fix common errors that you might encounter when you're rotating Secrets Manager secrets\.

Rotating secrets in AWS Secrets Manager requires you to use a Lambda function that defines how to interact with the database or service that owns the secret\.

**Topics**
+ [I want to find the diagnostic logs for my Lambda rotation function](#tshoot-rotation-find-logs)
+ [I get "access denied" when trying to configure rotation for my secret](#tshoot-lambda-initialconfig-perms)
+ [My first rotation fails after I enable rotation](#tshoot-lambda-initialconfig-mastersecret)
+ [Rotation fails because the secret value is not formatted as expected by the rotation function\.](#tshoot-lambda-mismatched-secretvalue)
+ [Secrets Manager says I successfully configured rotation, but the password isn't rotating](#tshoot-lambda-connection-with-internet)
+ [Rotation fails with an "Internal failure" error message](#tshoot-lambda-missingexcludedchars)
+ [CloudTrail shows access\-denied errors during rotation](#tshoot-lambda-accessdeniedduringrotation)
+ [My database requires an SSL/TLS connection but the Lambda rotation function isn't using SSL/TLS](#tshoot-lambda-SSLTLS)

## I want to find the diagnostic logs for my Lambda rotation function<a name="tshoot-rotation-find-logs"></a>

When the rotation function doesn't operate the way you expect, you should first check the CloudWatch logs\. Secrets Manager provides template code for the Lambda rotation function, and this code writes error messages to the CloudWatch log\.

**To view the CloudWatch logs for your Lambda function**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose your secret, and then on the details page, under **Rotation configuration**, choose the Lambda rotation function\. The Lambda console opens\.

1. On the **Monitor** tab, choose **Logs**, and then choose **View logs in CloudWatch**\.

   The CloudWatch console opens and displays the logs for your function\.

## I get "access denied" when trying to configure rotation for my secret<a name="tshoot-lambda-initialconfig-perms"></a>

When you add a Lambda rotation function Amazon Resource Name \(ARN\) to your secret, Secrets Manager checks the permissions of the function\. The role policy for the function must grant the Secrets Manager service principal `secretsmanager.amazonaws.com` permission to invoke the function \(`lambda:InvokeFunction`\)\. 

You can add this permission by running the following AWS CLI command:

```
aws lambda add-permission --function-name ARN_of_lambda_function --principal secretsmanager.amazonaws.com --action lambda:InvokeFunction --statement-id SecretsManagerAccess
```

## My first rotation fails after I enable rotation<a name="tshoot-lambda-initialconfig-mastersecret"></a>

When you enable rotation for a secret that uses a "master" secret to change the credentials on the secured service, Secrets Manager automatically configures most elements required for rotation\. However, Secrets Manager can't automatically grant permission to read the master secret to your Lambda function\. You must explicitly grant this permission yourself\. Specifically, you grant the permission by adding it to the policy attached to the IAM role attached to your Lambda rotation function\. That policy must include the following statement; this is only a statement, not a complete policy\. For the complete policy, see the second sample policy in the section [CloudTrail shows access\-denied errors during rotation](#tshoot-lambda-accessdeniedduringrotation)\.

```
{
    "Sid": "AllowAccessToMasterSecret",
    "Effect": "Allow",
    "Action": "secretsmanager:GetSecretValue",
    "Resource": "ARN_of_master_secret"
}
```

This enables the rotation function to retrieve the credentials from the master secret—then use the master secret credentials to change the credentials for the rotating secret\. 

## Rotation fails because the secret value is not formatted as expected by the rotation function\.<a name="tshoot-lambda-mismatched-secretvalue"></a>

Rotation might also fail if you don't format the secret value as a JSON structure as expected by the rotation function\. The rotation function you use determines the format used\. For the details of what each rotation function requires for the secret value, see the **Expected SecretString Value** entry under the relevant rotation function at [Secrets Manager rotation function templates](reference_available-rotation-templates.md)\.

For example, if you use the MySQL Single User rotation function, the `SecretString` text structure must look like this:

```
{
  "engine": "mysql",
  "host": "<required: instance host name/resolvable DNS name>",
  "username": "<required: username>",
  "password": "<required: password>",
  "dbname": "<optional: database name. If not specified, defaults to None>",
  "port": "<optional: TCP port number. If not specified, defaults to 3306>"
}
```

## Secrets Manager says I successfully configured rotation, but the password isn't rotating<a name="tshoot-lambda-connection-with-internet"></a>

This can occur if there are network configuration issues that prevent the Lambda function from communicating with either your secured database/service or the Secrets Manager service endpoint, on the public Internet\. If you run your database or service in a VPC, then you use one of two options for configuration:
+ Make the database in the VPC publicly accessible with an Amazon EC2 Elastic IP address\.
+ Configure the Lambda rotation function to operate in the same VPC as the database/service\.
+ If your VPC doesn't have access to the public Internet, for example, if you don't [configure the VPC with a NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) for access, then you must [configure the VPC with a private service endpoint for Secrets Manager](rotation-network-rqmts.md) accessible from within the VPC\.

To determine if this type of configuration issue caused the rotation failure, perform the following steps\.

**To diagnose connectivity issues between your rotation function and the database or Secrets Manager**

1. Open your logs by following the procedure [I want to find the diagnostic logs for my Lambda rotation function](#tshoot-rotation-find-logs)\.

1. Examine the log files to look for indications that timeouts occurr between either the Lambda function and the AWS Secrets Manager service, or between the Lambda function and the secured database or service\.

1. For information about how to configure services and Lambda functions to interoperate within the VPC environment, see the [Amazon Virtual Private Cloud documentation](https://aws.amazon.com/documentation/vpc/) and the [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/) \.

## Rotation fails with an "Internal failure" error message<a name="tshoot-lambda-missingexcludedchars"></a>

When your rotation function generates a new password and attempts to store it in the database as a new set of credentials, you must ensure the password includes only characters valid for the specified database\. The attempt to set the password for a user fails if the password includes characters that the database engine doesn't accept\. This error appears as an "internal failure"\. Refer to the database documentation for a list of the characters you can use\. Then, exclude all others by using the [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetRandomPassword.html#SecretsManager-GetRandomPassword-request-ExcludeCharacters](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetRandomPassword.html#SecretsManager-GetRandomPassword-request-ExcludeCharacters) parameter in the `GetRandomPassword` API call\.

## CloudTrail shows access\-denied errors during rotation<a name="tshoot-lambda-accessdeniedduringrotation"></a>

When you configure rotation, if you let Secrets Manager create the rotation function for you, Secrets Manager automatically provides a policy attached to the function IAM role that grants the appropriate permissions\. If you create a custom function, you need to grant the following permissions to the role attached to the function\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "secretsmanager:DescribeSecret",
                "secretsmanager:GetRandomPassword",
                "secretsmanager:GetSecretValue",
                "secretsmanager:PutSecretValue",
                "secretsmanager:UpdateSecretVersionStage"
            ],
            "Resource": "*"
        }
    ]
}
```

Also, if your rotation uses separate master secret credentials to rotate this secret, then you must also grant permission to retrieve the secret value from the master secret\. For more information, see [My first rotation fails after I enable rotation](#tshoot-lambda-initialconfig-mastersecret)\. The combined policy might look like this:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowAccessToSecretsManagerAPIs",
            "Effect": "Allow",
            "Action": [
                "secretsmanager:DescribeSecret",
                "secretsmanager:GetRandomPassword",
                "secretsmanager:GetSecretValue",
                "secretsmanager:PutSecretValue",
                "secretsmanager:UpdateSecretVersionStage"
            ],
            "Resource": "*"
        },
        {
            "Sid": "AllowAccessToMasterSecret",
            "Effect": "Allow",
            "Action": "secretsmanager:GetSecretValue",
            "Resource": "MasterSecretArn"
        }
    ]
}
```

## My database requires an SSL/TLS connection but the Lambda rotation function isn't using SSL/TLS<a name="tshoot-lambda-SSLTLS"></a>

If your database requires an SSL/TLS connection, but the rotation function uses an unencrypted connection, the rotation function can't connect to the database, and rotation fails\. In Amazon CloudWatch, the rotation function logs one of the following errors:
+ For single\-user rotation:

  `setSecret: Unable to log into database with previous, current, or pending secret of secret arn SecretArn`
+ For multi\-user rotation:

  `setSecret: Unable to log into database using current credentials for secret SecretArn`

Rotation functions for Amazon RDS \(except Oracle\) and Amazon DocumentDB automatically use Secure Socket Layer \(SSL\) or Transport Layer Security \(TLS\) to connect to your database, if it is available\. Otherwise they use an unencrypted connection\.<a name="rotation-function-created-date"></a>

**Note**  
If you set up automatic secret rotation before December 20, 2021, your rotation function might be based on an older template that did not support SSL/TLS\. To support connections that use SSL/TLS, you need to [recreate your rotation function](rotate-secrets_turn-on-for-db.md)\.

**To determine when your rotation function was created**

1. In the Secrets Manager console [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/), open your secret\. In the **Rotation configuration** section, under **Lambda rotation function**, you see the **Lambda function ARN**, for example, `arn:aws:lambda:aws-region:123456789012:function:SecretsManagerMyRotationFunction`\. Copy the function name from the end of the ARN, in this example `SecretsManagerMyRotationFunction`\.

1. In the AWS Lambda console [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/), under **Functions**, paste your Lambda function name in the search box, choose Enter, and then choose the Lambda function\. 

1. In the function details page, on the **Configuration** tab, under **Tags**, copy the value next to the key **aws:cloudformation:stack\-name**\.

1. In the AWS CloudFormation console [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/), under **Stacks**, paste the key value in the search box, and then choose Enter\.

1. The list of stacks filters so that only the stack that created the Lambda rotation function appears\. In the **Created date** column, view the date the stack was created\. This is the date the Lambda rotation function was created\.