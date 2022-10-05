# Set up automatic rotation for AWS Secrets Manager secrets using the AWS CLI<a name="rotate-secrets-cli"></a>

Rotation is the process of periodically updating a secret\. When you rotate a secret, you update the credentials in both the secret and the database or service that the secret is for\.

Secrets Manager uses Lambda functions to rotate secrets\. For an overview, see [How rotation works](rotating-secrets.md#rotate-secrets_how)\.

You can also use the console to set up rotation\. For more information, see [Automatic rotation \(console\)](rotate-secrets_turn-on-for-other.md)\.

To set up rotation using the AWS CLI, if you are rotating an Amazon RDS, Amazon Redshift, or Amazon DocumentDB secret, you first need to choose a [Rotation strategy](getting-started.md#rotation-stragegy)\. If you choose the *alternating users strategy*, you must store a separate secret with credentials for a database superuser\. Next, you write the rotation function code\. Secrets Manager provides templates you can base your function on\. Then you create a Lambda function with your code and set permissions for both the Lambda function and the Lambda execution role\. The next step is to make sure that the Lambda rotation function can access both Secrets Manager and your database or service through the network\. Finally, you configure the secret for rotation\. 

To turn on automatic rotation, you must have permission to create the IAM execution role and attach a permission policy to it\. You need both `iam:CreateRole` and `iam:AttachRolePolicy` permissions\. 

**Warning**  
Granting an identity both `iam:CreateRole` and `iam:AttachRolePolicy` permissions allows the identity to grant themselves any permissions\.

**Topics**
+ [\(Optional\) Step 1: Create a superuser secret](#rotate-secrets-cli_step1)
+ [Step 2: Write the rotation function code](#rotate-secrets-cli_step2)
+ [Step 3: Create the Lambda function and execution role](#rotate-secrets-cli_step3)
+ [Step 4: Set up network access](#rotate-secrets-cli_step4)
+ [Step 5: Configure the secret for rotation](#rotate-secrets-cli_step5)
+ [Next steps](#rotate-secrets-cli_stepnext)

## \(Optional\) Step 1: Create a superuser secret<a name="rotate-secrets-cli_step1"></a>

For Amazon RDS, Amazon Redshift, and Amazon DocumentDB, Secrets Manager offers two rotation strategies:

**Single user rotation strategy**  
This strategy updates credentials for one user in one secret\. The user must have permission to update their password\. This is the simplest rotation strategy, and it is appropriate for most use cases\.   
When the secret rotates, open database connections are not dropped\. While rotation is happening, there is a short period of time between when the password in the database changes and when the secret is updated\. During this time, there is a low risk of the database denying calls that use the rotated credentials\. You can mitigate this risk with an [appropriate retry strategy](http://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/)\. After rotation, new connections use the new credentials\. 

**Alternating users rotation strategy**  
This strategy updates credentials for two users in one secret\. You create the first user, and during the first rotation, the rotation function clones it to create the second user\. Every time the secret rotates, the rotation function alternates which user's password it updates\. Because most users don't have permission to clone themselves, you must provide the credentials for a `superuser` in another secret\. If cloned users in your database don't have the same permissions as the original user, often called an *ad hoc* or *interactive* user, we recommend using the single\-user rotation strategy\.  
This strategy is appropriate for databases with permission models where one role owns the database tables and a second role has permission to access the database tables\. It is also appropriate for applications that require high availability\. If an application retrieves the secret during rotation, the application still gets a valid set of credentials\. After rotation, both `user` and `user_clone` credentials are valid\. There is even less chance of applications getting a deny during this type of rotation than single user rotation\. If the database is hosted on a server farm where the password change takes time to propagate to all servers, there is a risk of the database denying calls that use the new credentials\. You can mitigate this risk with an [appropriate retry strategy](http://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/)\.   
If you choose the *alternating users strategy*, you must [Create a database secret](create_database_secret.md) and store database superuser credentials in it\. You need a secret with superuser credentials because rotation clones the first user, and most users do not have that permission\. 

## Step 2: Write the rotation function code<a name="rotate-secrets-cli_step2"></a>

To rotate a secret, you need a rotation function\. A rotation function is a Lambda function that Secrets Manager calls to rotate your secret\.

For a function that can rotate an Amazon RDS, Amazon Redshift, or Amazon DocumentDB secret, you can copy the code from the [appropriate template supplied by Secrets Manager](reference_available-rotation-templates.md)\.

For all other types of secrets, use the [generic rotation template](reference_available-rotation-templates.md#OTHER_rotation_templates) as a starting point to write your own rotation function\. 

Save your rotation function in a ZIP file *my\-function\.zip* along with any required dependencies\.

As you write your function, be cautious about including debugging or logging statements\. These statements can cause information in your function to be written to Amazon CloudWatch, so you need to make sure the log doesn't include any sensitive data from the include sensitive information collected during development\.

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
+ [`create_secret`](#w445aac19c13c19c27)
+ [`set_secret`](#w445aac19c13c19c29)
+ [`test_secret`](#w445aac19c13c19c31)
+ [`finish_secret`](#w445aac19c13c19c33)

### `create_secret`<a name="w445aac19c13c19c27"></a>

In `create_secret`, you first check if a secret exists by calling [https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.get_secret_value](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.get_secret_value) with the passed\-in `ClientRequestToken`\. If there's no secret, you create a new secret with [https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.create_secret](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.create_secret) and the token as the `VersionId`\. Then you can generate a new secret value with [https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.get_random_password](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.get_random_password)\. You must ensure the new secret value only includes characters that are valid for the database or service\. Exclude characters by using the `ExcludeCharacters` parameter\. Call [https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.put_secret_value](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.put_secret_value) to store it with the staging label `AWSPENDING`\. Storing the new secret value in `AWSPENDING` helps ensure idempotency\. If rotation fails for any reason, you can refer to that secret value in subsequent calls\. See [How do I make my Lambda function idempotent](https://aws.amazon.com/premiumsupport/knowledge-center/lambda-function-idempotent/)\.

As you test your function, use the AWS CLI to see version stages: call [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/describe-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/describe-secret.html) and look at `VersionIdsToStages`\.

### `set_secret`<a name="w445aac19c13c19c29"></a>

In `set_secret`, you change the credential in the database or service to match the new secret value in the `AWSPENDING` version of the secret\. 

For you pass statements to a service that interprets statements, like a database, use query parameterization For more information, see [Query Parameterization Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Query_Parameterization_Cheat_Sheet.html) on the *OWASP web site*\.

The rotation function is a privileged deputy that has the authorization to access and modify customer credentials in both the Secrets Manager secret and the target resource\. To prevent a potential [confused deputy attack](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html), you need to make sure that an attacker cannot use the function to access other resources\. Before you update the credential:
+ Check that the credential in the `AWSCURRENT` version of the secret is valid\. If the `AWSCURRENT` credential isn't valid, abandon the rotation attempt\.
+ Check that the `AWSCURRENT` and `AWSPENDING` secret values are for the same resource\. For a username and password, check that the `AWSCURRENT` and `AWSPENDING` usernames are the same\. 

  Also check that the destination service resource is the same\. For a database, check that the `AWSCURRENT` and `AWSPENDING` host names are the same\.

### `test_secret`<a name="w445aac19c13c19c31"></a>

In `test_secret`, you test the `AWSPENDING` version of the secret by using it to access the database or service\.

### `finish_secret`<a name="w445aac19c13c19c33"></a>

In `finish_secret`, you use [https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.update_secret_version_stage](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html#SecretsManager.Client.update_secret_version_stage) to move the staging label `AWSCURRENT` from the previous secret version to the new secret version\. Secrets Manager automatically adds the `AWSPREVIOUS` staging label to the previous version, so that you retain the last known good version of the secret\. 

## Step 3: Create the Lambda function and execution role<a name="rotate-secrets-cli_step3"></a>

A [Lambda execution role](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html) is a role that Lambda assumes when the function is invoked\.

**To create a Lambda rotation function and execution role**

1. Create a trust policy for the Lambda execution role and save it as a JSON file\. For examples, see [Permissions for rotation](rotating-secrets-required-permissions-function.md)\. The policy must:
   + Allow the role to call Secrets Manager operations on the secret\. 
   + Allow the role to use the KMS key if the secret is encrypted with a key other than `aws/secretsmanager`\.
   + Allow the role to call the service that the secret is for\. 

1. Create the Lambda execution role and apply the trust policy by calling [https://docs.aws.amazon.com/cli/latest/reference/iam/create-role.html](https://docs.aws.amazon.com/cli/latest/reference/iam/create-role.html)\.

   ```
   aws iam create-role \
               --role-name rotation-lambda-role \
               --assume-role-policy-document file://trust-policy.json
   ```

1. Create the Lambda function from the ZIP file by calling [https://docs.aws.amazon.com/cli/latest/reference/lambda/create-function.html](https://docs.aws.amazon.com/cli/latest/reference/lambda/create-function.html)\.

   ```
   aws lambda create-function \
       --function-name my-rotation-function \
       --runtime python3.9 \
       --zip-file fileb://my-function.zip \
       --handler my-handler \
       --role arn:aws:iam::123456789012:role/service-role/rotation-lambda-role
   ```

1. Set a resource policy on the Lambda function to allow Secrets Manager to invoke it by calling [https://docs.aws.amazon.com/cli/latest/reference/lambda/add-permission.html](https://docs.aws.amazon.com/cli/latest/reference/lambda/add-permission.html)\. The example command includes `source-account` to help prevent Lambda from being used as a [confused deputy](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html)\.

   ```
   aws lambda add-permission \
       --function-name my-rotation-function \
       --action lambda:InvokeFunction \
       --statement-id SecretsManager \
       --principal secretsmanager.amazonaws.com \
       --source-account 123456789012
   ```

## Step 4: Set up network access<a name="rotate-secrets-cli_step4"></a>

To be able to rotate a secret, the Lambda rotation function must be able to access both the secret and the database or service\.

**To access a secret**  
Your Lambda rotation function must be able to access a Secrets Manager endpoint\. If your Lambda function can access the internet, then you can use a public endpoint\. To find an endpoint, see [AWS Secrets Manager endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/asm.html)\.  
If your Lambda function runs in a VPC that doesn't have internet access, we recommend you configure Secrets Manager service private endpoints within your VPC\. Your VPC can then intercept requests addressed to the public regional endpoint and redirect them to the private endpoint\. For more information, see [VPC endpoint](vpc-endpoint-overview.md)\.  
Alternatively, you can enable your Lambda function to access a Secrets Manager public endpoint by adding a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) or an [internet gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) to your VPC, which allows traffic from your VPC to reach the public endpoint\. This exposes your VPC to more risk because an IP address for the gateway can be attacked from the public Internet\.

**To access the database or service**  
If your database or service is running on an Amazon EC2 instance in a VPC, we recommend that you configure your Lambda function to run in the same VPC\. Then the rotation function can communicate directly with your service\. For more information, see [Configuring VPC access](https://docs.aws.amazon.com/lambda/latest/dg/configuration-vpc.html#vpc-configuring)\.  
To allow the Lambda function to access the database or service, you must make sure that the security groups attached to your Lambda rotation function allow outbound connections to the database or service\. You must also make sure that the security groups attached to your database or service allow inbound connections from the Lambda rotation function\. 

## Step 5: Configure the secret for rotation<a name="rotate-secrets-cli_step5"></a>

To turn on automatic rotation for your secret, call [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/rotate-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/rotate-secret.html)\. You can set a rotation schedule with a `cron()` or `rate()` schedule expression, and you can set a rotation window duration\. For more information, see [Schedule expressions](rotate-secrets_schedule.md)\.

```
aws secretsmanager rotate-secret \
    --secret-id MySecret \
    --rotation-lambda-arn arn:aws:lambda:Region:123456789012:function:my-rotation-function \
    --rotation-rules "{\"ScheduleExpression\": \"cron(0 16 1,15 * ? *)\", \"Duration\": \"2h\"}"
```

## Next steps<a name="rotate-secrets-cli_stepnext"></a>

See [Troubleshoot AWS Secrets Manager rotation](troubleshoot_rotation.md)\.