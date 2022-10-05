# Troubleshoot AWS Secrets Manager rotation<a name="troubleshoot_rotation"></a>

Secrets Manager uses a Lambda function to rotate secrets\. For more information, see [How rotation works](rotating-secrets.md#rotate-secrets_how)\. The Lambda rotation function interacts with the database or service the secret is for as well as Secrets Manager\. When rotation doesn't work the way you expect, you should first check the CloudWatch logs\.

**To view the CloudWatch logs for your Lambda function**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose your secret, and then on the details page, under **Rotation configuration**, choose the Lambda rotation function\. The Lambda console opens\.

1. On the **Monitor** tab, choose **Logs**, and then choose **View logs in CloudWatch**\. 

   The CloudWatch console opens and displays the logs for your function\.

**Topics**
+ [No activity after "Found credentials in environment variables"](#troubleshoot_rotation_timing-out)
+ [No activity after "createSecret"](#troubleshoot_rotation_createSecret)
+ [Error: "Key is missing from secret JSON"](#tshoot-lambda-mismatched-secretvalue)
+ [Error: "setSecret: Unable to log into database"](#troubleshoot_rotation_setSecret)

## No activity after "Found credentials in environment variables"<a name="troubleshoot_rotation_timing-out"></a>

If there is no activity after "Found credentials in environment variables", and the task duration is long, for example the default Lambda timeout of 30000ms, then the Lambda function may be timing out while trying to reach the Secrets Manager endpoint\.

Your Lambda rotation function must be able to access a Secrets Manager endpoint\. If your Lambda function can access the internet, then you can use a public endpoint\. To find an endpoint, see [AWS Secrets Manager endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/asm.html)\.

If your Lambda function runs in a VPC that doesn't have internet access, we recommend you configure Secrets Manager service private endpoints within your VPC\. Your VPC can then intercept requests addressed to the public regional endpoint and redirect them to the private endpoint\. For more information, see [VPC endpoint](vpc-endpoint-overview.md)\.

Alternatively, you can enable your Lambda function to access a Secrets Manager public endpoint by adding a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) or an [internet gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) to your VPC, which allows traffic from your VPC to reach the public endpoint\. This exposes your VPC to more risk because an IP address for the gateway can be attacked from the public Internet\.

## No activity after "createSecret"<a name="troubleshoot_rotation_createSecret"></a>

The following are issues that can cause rotation to stop after createSecret:

**The VPC Network ACLs do not allow HTTPS traffic in and out\.**  
For more information, see [Control traffic to subnets using Network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html) in the *Amazon VPC User Guide*\.

**Lambda function timeout configuration is too short to perform the task\. **  
For more information, see [Configuring Lambda function options](https://docs.aws.amazon.com/lambda/latest/dg/configuration-function-common.html) in the *AWS Lambda Developer Guide*\.

**The Secrets Manager VPC endpoint does not allow the VPC CIDRs on ingress in the assigned security groups\. **  
For more information, see [Control traffic to resources using security groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon VPC User Guide*\.

**The Secrets Manager VPC endpoint policy does not allow Lambda to use the VPC endpoint\. **  
For more information, see [Creating a VPC endpoint policy for Secrets Manager](vpc-endpoint-overview.md#vpc-endpoint-policy)\.

## Error: "Key is missing from secret JSON"<a name="tshoot-lambda-mismatched-secretvalue"></a>

A Lambda rotation function requires the secret value to be in a specific JSON structure\. If you see this error, then the JSON might be missing a key that the rotation function tried to access\. For information about the JSON structure for each type of secret, see [JSON structure of AWS Secrets Manager database credential secrets ](reference_secret_json_structure.md)\.

## Error: "setSecret: Unable to log into database"<a name="troubleshoot_rotation_setSecret"></a>

The following are issues that can cause this error:

**The rotation function can't access the database\.**  
If the task duration is long, for example over 5000ms, then the Lambda rotation function might not be able to access the database over the network\.   
If your database or service is running on an Amazon EC2 instance in a VPC, we recommend that you configure your Lambda function to run in the same VPC\. Then the rotation function can communicate directly with your service\. For more information, see [Configuring VPC access](https://docs.aws.amazon.com/lambda/latest/dg/configuration-vpc.html#vpc-configuring)\.  
To allow the Lambda function to access the database or service, you must make sure that the security groups attached to your Lambda rotation function allow outbound connections to the database or service\. You must also make sure that the security groups attached to your database or service allow inbound connections from the Lambda rotation function\. 

**The credentials in the secret are incorrect\.**  
If the task duration is short, then the Lambda rotation function might not be able to authenticate with the credentials in the secret\. Check the credentials by logging in manually with the information in the `AWSCURRENT` and `AWSPREVIOUS` versions of the secret using the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html)\.

**The database requires SSL/TLS access\.**  
If your database requires an SSL/TLS connection, but the rotation function uses an unencrypted connection, then the rotation function can't connect to the database\. Rotation functions for Amazon RDS \(except Oracle\) and Amazon DocumentDB automatically use Secure Socket Layer \(SSL\) or Transport Layer Security \(TLS\) to connect to your database, if it is available\. Otherwise they use an unencrypted connection\.  
If you set up automatic secret rotation before December 20, 2021, your rotation function might be based on an older template that did not support SSL/TLS\. To support connections that use SSL/TLS, you need to [recreate your rotation function](rotate-secrets_turn-on-for-db.md)\. 

**To determine when your rotation function was created**

1. In the Secrets Manager console [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/), open your secret\. In the **Rotation configuration** section, under **Lambda rotation function**, you see the **Lambda function ARN**, for example, `arn:aws:lambda:aws-region:123456789012:function:SecretsManagerMyRotationFunction `\. Copy the function name from the end of the ARN, in this example ` SecretsManagerMyRotationFunction `\. 

1. In the AWS Lambda console [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/), under **Functions**, paste your Lambda function name in the search box, choose Enter, and then choose the Lambda function\. 

1. In the function details page, on the **Configuration** tab, under **Tags**, copy the value next to the key **aws:cloudformation:stack\-name**\. 

1. In the AWS CloudFormation console [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/), under **Stacks**, paste the key value in the search box, and then choose Enter\.

1. The list of stacks filters so that only the stack that created the Lambda rotation function appears\. In the **Created date** column, view the date the stack was created\. This is the date the Lambda rotation function was created\.