# Troubleshooting AWS Secrets Manager Rotation of Secrets<a name="troubleshoot_rotation"></a>

Use the information here to help you diagnose and fix common errors that you might encounter when you're rotating Secrets Manager secrets\.

Rotating secrets in AWS Secrets Manager requires you to use a Lambda function that defines how to interact with the database or service that owns the secret\.

**Topics**
+ [I want to find the diagnostic logs for my Lambda rotation function](#tshoot-rotation-find-logs)
+ [I can't predict when rotation will start](#tshoot-scheduling-rotation)
+ [I get "access denied" when trying to configure rotation for my secret](#tshoot-lambda-initialconfig-perms)
+ [My first rotation fails after I enable rotation](#tshoot-lambda-initialconfig-mastersecret)
+ [Secrets Manager says I successfully configured rotation, but the password isn't rotating](#tshoot-lambda-connection-with-internet)
+ [CloudTrail shows access\-denied errors during rotation](#tshoot-lambda-accessdeniedduringrotation)

## I want to find the diagnostic logs for my Lambda rotation function<a name="tshoot-rotation-find-logs"></a>

When the rotation function isn't operating the way that you expect, the first thing you should check are the logs\. Secrets Manager provides template code for the Lambda rotation function, and this code writes error messages to the CloudWatch log\.

**To view the CloudWatch logs for your Lambda function**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. From the list of functions, choose the name of the Lambda function that's associated with your secret\.

1. Choose the **Monitoring** tab\.

1. In the **Invocation errors** section, choose **Jump to Logs**\.

   The CloudWatch console opens and displays the logs for your function\.

## I can't predict when rotation will start<a name="tshoot-scheduling-rotation"></a>

You can predict only the date of the next rotation, not the time\.

Secrets Manager schedules the next rotation when the previous one is complete\. Secrets Manager schedules the date by adding the rotation interval \(number of days\) to the actual date of the last rotation\. The service chooses the hour within that 24\-hour date window randomly\. The minute is also chosen somewhat randomly, but weighted towards the top of the hour and influenced by a variety of factors that help distribute load\.

## I get "access denied" when trying to configure rotation for my secret<a name="tshoot-lambda-initialconfig-perms"></a>

When you add a Lambda rotation function's Amazon Resource Name \(ARN\) to your secret, Secrets Manager checks the permissions of the function\. The role policy for the function must grant the Secrets Manager service principal `secretsmanager.amazonaws.com` permission to invoke the function \(`lambda:InvokeFunction`\)\. 

You can add this permission by running the following AWS CLI command:

```
aws lambda add-permission --function-name ARN_of_lambda_function --principal secretsmanager.amazonaws.com --action lambda:InvokeFunction --statement-id SecretsManagerAccess
```

## My first rotation fails after I enable rotation<a name="tshoot-lambda-initialconfig-mastersecret"></a>

When you enable rotation for a secret that uses a "master" secret to change the credentials on the secured service, Secrets Manager automatically configures most elements that are required for rotation\. However, Secrets Manager can't automatically grant permission to read the master secret to your Lambda function\. You must explicitly grant this permission yourself\. Specifically, you grant the permission by adding it to the policy attached to the IAM role that's attached to your Lambda rotation function\. That policy must include the following statement \(this is only a statement, not a complete policy\)\. For the complete policy, see the second sample policy in the section [CloudTrail shows access\-denied errors during rotation](#tshoot-lambda-accessdeniedduringrotation)\.

```
{
    "Sid": "AllowAccessToMasterSecret",
    "Effect": "Allow",
    "Action": "secretsmanager:GetSecretValue",
    "Resource": "ARN_of_master_secret"
}
```

This enables the rotation function to retrieve the credentials from the master secret—which it can then use to change the credentials for the secret being rotated\. 

## Secrets Manager says I successfully configured rotation, but the password isn't rotating<a name="tshoot-lambda-connection-with-internet"></a>

This can occur if there are network configuration issues that prevent the Lambda function from communicating with either your secured database/service or the Secrets Manager service endpoint \(which is on the public internet\)\. If your database/service is running in a VPC, then you can configure things one of two ways:
+ Make the database in the VPC publicly accessible with an Amazon EC2 Elastic IP address\.
+ Configure the Lambda rotation function to operate in the same VPC as the database/service\.
+ If your VPC doesn't have access to the public internet \(for example, if you don't [configure the VPC with a NAT gateway](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-nat-gateway.html) for access\), then you must [configure the VPC with a private service endpoint for Secrets Manager](rotation-network-rqmts.md) that's accessible from within the VPC\.

To determine if this type of configuration issue is the cause of the rotation failure, perform the following steps\.

**To diagnose connectivity issues between your rotation function and the database or Secrets Manager**

1. Open your logs by following the procedure [I want to find the diagnostic logs for my Lambda rotation function](#tshoot-rotation-find-logs)\.

1. Examine the log files to look for indications that there are timeouts occurring between either the Lambda function and the AWS Secrets Manager service, or between the Lambda function and the secured database or service\.

1. For information about how to configure services and Lambda functions to interoperate within the VPC environment, see the [Amazon Virtual Private Cloud documentation](https://aws.amazon.com/documentation/vpc/) and the [AWS Lambda Developer Guide](http://docs.aws.amazon.com/lambda/latest/dg/) \.

## CloudTrail shows access\-denied errors during rotation<a name="tshoot-lambda-accessdeniedduringrotation"></a>

When you configure rotation, if you let Secrets Manager create the rotation function for you, it's automatically provided with a policy attached to the function's IAM role that grants the appropriate permissions\. If you create the function on your own, you need to grant the following permissions to the role that's attached to the function\.

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
                "secretsmanager:UpdateSecretVersionStage",
            ],
            "Resource": "*"
        }
    ]
}
```

[Suggest improvements to this example on GitHub\.](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/iam_policies/secretsmanager/asm-official-role-policy-for-rotation-function-single-secret.json)

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
                "secretsmanager:UpdateSecretVersionStage",
            ],
            "Resource": "*"
        },
        {
            "Sid": "AllowAccessToMasterSecret",
            "Effect": "Allow",
            "Action": "secretsmanager:GetSecretValue",
            "Resource": "<arn_of_master_secret>"
        }
    ]
}
```

[Suggest improvements to this example on GitHub\.](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/iam_policies/secretsmanager/asm-official-role-policy-for-rotation-function-master-secret.json)