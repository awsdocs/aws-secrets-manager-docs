# Set up automatic rotation for AWS Secrets Manager secrets using the console<a name="rotate-secrets_turn-on-for-other"></a>

Rotation is the process of periodically updating a secret\. When you rotate a secret, you update the credentials in both the secret and the database or service that the secret is for\.

Secrets Manager uses Lambda functions to rotate secrets\. For an overview, see [How rotation works](rotating-secrets.md#rotate-secrets_how)\.

You can also use the AWS CLI to set up rotation\. For more information, see [Automatic rotation \(AWS CLI\)](rotate-secrets-cli.md)\.

To set up rotation using the console, you first configure the secret for rotation\. During that step, you also create an empty Lambda rotation function\. Next, you set permissions for the rotation function and for the Lambda execution role\. Then you write the rotation function code\. The last step is to make sure that the Lambda rotation function can access both Secrets Manager and your database or service through the network\.

For database secrets, see [Set up automatic rotation for Amazon RDS, Amazon Redshift, or Amazon DocumentDB secrets using the console](rotate-secrets_turn-on-for-db.md)\.

To turn on automatic rotation, you must have permission to create the IAM execution role and attach a permission policy to it\. You need both `iam:CreateRole` and `iam:AttachRolePolicy` permissions\. 

**Warning**  
Granting an identity both `iam:CreateRole` and `iam:AttachRolePolicy` permissions allows the identity to grant themselves any permissions\.

**Topics**
+ [Step 1: Configure the secret for rotation](#rotate-secrets_turn-on-for-other_step1)
+ [Step 2: Set permissions for the rotation function](#rotate-secrets_turn-on-for-other_step2)
+ [Step 3: \(Optional\) Set an additional permissions condition on the rotation function](#rotate-secrets_turn-on-for-other_step3)
+ [Step 4: Set up network access for the rotation function](#rotate-secrets_turn-on-for-other_step4)
+ [Step 5: Write the rotation function code](#rotate-secrets_turn-on-for-other_step5)
+ [Next steps](#rotate-secrets_turn-on-for-other_stepnext)

## Step 1: Configure the secret for rotation<a name="rotate-secrets_turn-on-for-other_step1"></a>

In this step, you set a rotation schedule for your secret and create an empty rotation function\. Your secret will not be rotated until you finish writing the rotation function\. If you schedule rotation before the rotation function is written, or if it fails for any reason, Secrets Manager will retry the rotation function multiple times\.

**To configure rotation and create an empty rotation function**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the **Secrets** page, choose your secret\.

1. On the **Secret details** page, in the **Rotation configuration** section, choose **Edit rotation**\. In the **Edit rotation configuration** dialog box, do the following:

   1. Turn on **Automatic rotation**\.

   1. Under **Rotation schedule**, enter your schedule in UTC time zone in either the **Schedule expression builder** or as a **Schedule expression**\. Secrets Manager stores your schedule as a `rate()` or `cron()` expression\. The rotation window automatically starts at midnight unless you specify a **Start time**\. For more information, see [Schedule expressions](rotate-secrets_schedule.md)\.

   1. \(Optional\) For **Window duration**, choose the length of the window during which you want Secrets Manager to rotate your secret, for example **3h** for a three hour window\. The window must not extend into the next rotation window\. If you don't specify **Window duration**, for a rotation schedule in hours, the window automatically closes after one hour\. For a rotation schedule in days, the window automatically closes at the end of the day\. 

   1. \(Optional\) Choose **Rotate immediately when the secret is stored** to rotate your secret when you save your changes\. If you clear the checkbox, then the first rotation will begin on the schedule you set\.

   1. Under **Rotation function**, choose **Create function**\. The Lambda console opens in a new window\.

      1. In the Lambda console, on the **Create function** page, do one of the following:
        + If you see **Browse serverless app repository**, choose it\.

          1. Under **Public applications**, in the search box, enter **SecretsManagerRotationTemplate**\.

          1. Choose **Show apps that create custom IAM roles or resource policies**\.

          1. Choose the **SecretsManagerRotationTemplate** tile\.

          1. On the **Review, configure and deploy** page, in the **Application settings** tile, fill in the required fields, and then choose **Deploy**\. For a list of endpoints, see [Secrets Manager endpoints ](asm_access.md#endpoints)\.
        + If you don't see **Browse serverless app repository**, your AWS Region might not support the AWS Serverless Application Repository\. Choose **Author from scratch**\.

          1. For **Function name**, enter a name for your rotation function\.

          1. For **Runtime**, choose **Python 3\.9**\.

          1. When the new Lambda function opens, scroll down to choose **Configuration**, and then on the left choose **Permissions**\.

          1. Scroll down to **Resource\-based policy** and choose **Add permissions** to grant permission for Secrets Manager to invoke the function\. To attach a resource policy to a Lambda function, see [Using resource\-based policies for Lambda](https://docs.aws.amazon.com/lambda/latest/dg/access-control-resource-based.html)\.

             The following policy shows how to allow Secrets Manager to invoke the Lambda function\.

             ```
             {
                 "Version": "2012-10-17",
                 "Id": "default",
                 "Statement": [
                 {
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

   1. Switch back to the Secrets Manager console to attach the new rotation function to your secret\. 

   1. For **Lambda rotation function**, choose the refresh button\. Then in the list of functions, choose your new function\.

   1. Choose **Save**\.

## Step 2: Set permissions for the rotation function<a name="rotate-secrets_turn-on-for-other_step2"></a>

The Lambda rotation function needs permission to access the secret in Secrets Manager, and it needs permission to access your database or service\. In this step, you grant these permissions to the Lambda execution role\. If the secret is encrypted with a KMS key other than the AWS managed key `aws/secretsmanager`, then you need to grant the Lambda execution role permission to use the key\. For policy examples, see [Permissions for rotation](rotating-secrets-required-permissions-function.md)\.

For instructions, see [Lambda execution role](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html) in the *AWS Lambda Developer Guide*\.

## Step 3: \(Optional\) Set an additional permissions condition on the rotation function<a name="rotate-secrets_turn-on-for-other_step3"></a>

In the resource policy for your rotation function, we recommend that you include the context key [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount) to help prevent Lambda from being used as a [confused deputy](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html)\. For some AWS services, to avoid the confused deputy scenario, AWS recommends that you use both the [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn) and [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount) global condition keys\. However, if you include the `aws:SourceArn` condition in your rotation function policy, the rotation function can only be used to rotate the secret specified by that ARN\. We recommend that you include only the context key `aws:SourceAccount` so that you can use the rotation function for multiple secrets\. 

**To update your rotation function resource policy**

1. In the Secrets Manager console, choose your secret, and then on the details page, under **Rotation configuration**, choose the Lambda rotation function\. The Lambda console opens\.

1. Follow the instructions at [Using resource\-based policies for Lambda](https://docs.aws.amazon.com/lambda/latest/dg/access-control-resource-based.html) to add a `aws:sourceAccount` condition\.

   ```
   "Condition": {
       "StringEquals": {
           "AWS:SourceAccount": "123456789012"
       },
   ```

## Step 4: Set up network access for the rotation function<a name="rotate-secrets_turn-on-for-other_step4"></a>

To be able to rotate a secret, the Lambda rotation function must be able to access the secret\. If your secret contains credentials, then the Lambda function must also be able to access the source of those credentials, such as a database or service\.

**To access a secret**  
Your Lambda rotation function must be able to access a Secrets Manager endpoint\. If your Lambda function can access the internet, then you can use a public endpoint\. To find an endpoint, see [Secrets Manager endpoints ](asm_access.md#endpoints)\.  
If your Lambda function runs in a VPC that doesn't have internet access, we recommend you configure Secrets Manager service private endpoints within your VPC\. Your VPC can then intercept requests addressed to the public regional endpoint and redirect them to the private endpoint\. For more information, see [VPC endpoint](vpc-endpoint-overview.md)\.  
Alternatively, you can enable your Lambda function to access a Secrets Manager public endpoint by adding a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) or an [internet gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) to your VPC, which allows traffic from your VPC to reach the public endpoint\. This exposes your VPC to more risk because an IP address for the gateway can be attacked from the public Internet\.

**\(Optional\) To access the database or service**  
For secrets such as API keys, there is no source database or service that you need to update along with the secret\.  
If your database or service is running on an Amazon EC2 instance in a VPC, we recommend that you configure your Lambda function to run in the same VPC\. Then the rotation function can communicate directly with your service\. For more information, see [Configuring VPC access](https://docs.aws.amazon.com/lambda/latest/dg/configuration-vpc.html#vpc-configuring)\.  
To allow the Lambda function to access the database or service, you must make sure that the security groups attached to your Lambda rotation function allow outbound connections to the database or service\. You must also make sure that the security groups attached to your database or service allow inbound connections from the Lambda rotation function\. 

## Step 5: Write the rotation function code<a name="rotate-secrets_turn-on-for-other_step5"></a>

The rotation function you created in Step 1 is a starting point for your function\. You write the code for your specific use case\. For a function that can rotate an Amazon ElastiCache secret, you can copy the code from the appropriate [template supplied by Secrets Manager](reference_available-rotation-templates.md)\.

As you write your function, be cautious about including debugging or logging statements\. These statements can cause information in your function to be written to Amazon CloudWatch, so you need to make sure the log doesn't include any sensitive information collected during development\.

For examples of log statements, see the [AWS Secrets Manager rotation function templates](reference_available-rotation-templates.md) source code\.

If you use external binaries and libraries, for example to connect to a resource, you need to manage patching them and keeping them up\-to\-date\.

For debugging suggestions, see [Testing and debugging serverless applications](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-test-and-debug.html)\.

**To open your Lambda rotation function for editing**

1. In the Secrets Manager console, choose your secret\.

1. In the **Rotation configuration** section, under **Lambda rotation function**, choose your rotation function\. 

   The Lambda console opens\. Scroll down to the **Code source** section\.

If your function doesn't already have it, copy the code from the [SecretsManagerRotationTemplate](reference_available-rotation-templates.md#OTHER_rotation_templates)\.

There are four steps to rotating a secret, which correspond to the following four methods of a Lambda rotation function\. 

**Topics**
+ [`create_secret`](#w162aac19c13c27c21)
+ [`set_secret`](#w162aac19c13c27c23)
+ [`test_secret`](#w162aac19c13c27c25)
+ [`finish_secret`](#w162aac19c13c27c27)

### `create_secret`<a name="w162aac19c13c27c21"></a>

In `create_secret`, you first check if a secret exists by calling [https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.get_secret_value](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.get_secret_value) with the passed\-in `ClientRequestToken`\. If there's no secret, you create a new secret with [https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.create_secret](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.create_secret) and the token as the `VersionId`\. Then you can generate a new secret value with [https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.get_random_password](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.get_random_password)\. You must ensure the new secret value only includes characters that are valid for the database or service\. Exclude characters by using the `ExcludeCharacters` parameter\. Call [https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.put_secret_value](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.put_secret_value) to store it with the staging label `AWSPENDING`\. Storing the new secret value in `AWSPENDING` helps ensure idempotency\. If rotation fails for any reason, you can refer to that secret value in subsequent calls\. See [How do I make my Lambda function idempotent](https://aws.amazon.com/premiumsupport/knowledge-center/lambda-function-idempotent/)\.

As you test your function, use the AWS CLI to see version stages: call [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/describe-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/describe-secret.html) and look at `VersionIdsToStages`\.

### `set_secret`<a name="w162aac19c13c27c23"></a>

In `set_secret`, you change the credential in the database or service to match the new secret value in the `AWSPENDING` version of the secret\. 

If you pass statements to a service that interprets statements, like a database, use query parameterization For more information, see [Query Parameterization Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Query_Parameterization_Cheat_Sheet.html) on the *OWASP web site*\.

The rotation function is a privileged deputy that has the authorization to access and modify customer credentials in both the Secrets Manager secret and the target resource\. To prevent a potential [confused deputy attack](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html), you need to make sure that an attacker cannot use the function to access other resources\. Before you update the credential:
+ Check that the credential in the `AWSCURRENT` version of the secret is valid\. If the `AWSCURRENT` credential isn't valid, abandon the rotation attempt\.
+ Check that the `AWSCURRENT` and `AWSPENDING` secret values are for the same resource\. For a username and password, check that the `AWSCURRENT` and `AWSPENDING` usernames are the same\. 
+ Check that the destination service resource is the same\. For a database, check that the `AWSCURRENT` and `AWSPENDING` host names are the same\.

### `test_secret`<a name="w162aac19c13c27c25"></a>

In `test_secret`, you test the `AWSPENDING` version of the secret by using it to access the database or service\.

### `finish_secret`<a name="w162aac19c13c27c27"></a>

In `finish_secret`, you use [https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.update_secret_version_stage](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.update_secret_version_stage) to move the staging label `AWSCURRENT` from the previous secret version to the new secret version\. Secrets Manager automatically adds the `AWSPREVIOUS` staging label to the previous version, so that you retain the last known good version of the secret\. 

## Next steps<a name="rotate-secrets_turn-on-for-other_stepnext"></a>

See [Troubleshoot AWS Secrets Manager rotation](troubleshoot_rotation.md)\.