# Set up automatic rotation for Amazon RDS, Amazon Redshift, or Amazon DocumentDB secrets using the console<a name="rotate-secrets_turn-on-for-db"></a>

Rotation is the process of periodically updating a secret\. When you rotate a secret, you update the credentials in both the secret and the database\. In Secrets Manager, you can set up automatic rotation for your database secrets\. For complete rotation tutorials, see [Single user rotation](tutorials_rotation-single.md) and [Alternating users rotation](tutorials_rotation-alternating.md)\.

Secrets Manager uses Lambda functions to rotate secrets\. For an overview, see [How rotation works](rotating-secrets.md#rotate-secrets_how)\.

**Tip**  
For some [Secrets managed by other services](service-linked-secrets.md), you use *managed rotation*\. To use [Managed rotation](rotate-secrets_managed.md), you first create the secret through the managing service\.

To set up rotation using the console, you need to first choose a rotation strategy\. Then you configure the secret for rotation, which creates a Lambda rotation function if you don't already have one\. The console also sets permissions for the Lambda function execution role\. The last step is to make sure that the Lambda rotation function can access both Secrets Manager and your database through the network\.

To turn on automatic rotation, you must have permission to create the IAM execution role and attach a permission policy to it\. You need both `iam:CreateRole` and `iam:AttachRolePolicy` permissions\. 

**Warning**  
Granting an identity both `iam:CreateRole` and `iam:AttachRolePolicy` permissions allows the identity to grant themselves any permissions\.

**Topics**
+ [Step 1: Choose a rotation strategy and \(optionally\) create a superuser secret](#rotate-secrets_turn-on-for-db_step1)
+ [Step 2: Configure rotation and create a rotation function](#rotate-secrets_turn-on-for-db_step2)
+ [Step 3: \(Optional\) Set an additional permissions condition on the rotation function](#rotate-secrets_turn-on-for-db_step3)
+ [Step 4: Set up network access for the rotation function](#rotate-secrets_turn-on-for-db_step4)
+ [Next steps](#rotate-secrets_turn-on-for-db_stepnext)

## Step 1: Choose a rotation strategy and \(optionally\) create a superuser secret<a name="rotate-secrets_turn-on-for-db_step1"></a>

For Amazon RDS, Amazon Redshift, and Amazon DocumentDB, Secrets Manager offers two rotation strategies:

**Single user rotation strategy**  
This strategy updates credentials for one user in one secret\. The user must have permission to update their password\. This is the simplest rotation strategy, and it is appropriate for most use cases\.   
When the secret rotates, open database connections are not dropped\. While rotation is happening, there is a short period of time between when the password in the database changes and when the secret is updated\. During this time, there is a low risk of the database denying calls that use the rotated credentials\. You can mitigate this risk with an [appropriate retry strategy](http://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/)\. After rotation, new connections use the new credentials\. 

**Alternating users rotation strategy**  
This strategy updates credentials for two users in one secret\. You create the first user, and during the first rotation, the rotation function clones it to create the second user\. Every time the secret rotates, the rotation function alternates which user's password it updates\. Because most users don't have permission to clone themselves, you must provide the credentials for a `superuser` in another secret\. If cloned users in your database don't have the same permissions as the original user, often called an *ad hoc* or *interactive* user, we recommend using the single\-user rotation strategy\.  
This strategy is appropriate for databases with permission models where one role owns the database tables and a second role has permission to access the database tables\. It is also appropriate for applications that require high availability\. If an application retrieves the secret during rotation, the application still gets a valid set of credentials\. After rotation, both `user` and `user_clone` credentials are valid\. There is even less chance of applications getting a deny during this type of rotation than single user rotation\. If the database is hosted on a server farm where the password change takes time to propagate to all servers, there is a risk of the database denying calls that use the new credentials\. You can mitigate this risk with an [appropriate retry strategy](http://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/)\.   
If you choose the *alternating users strategy*, you must [Create a database secret](create_database_secret.md) and store database superuser credentials in it\. You need a secret with superuser credentials because rotation clones the first user, and most users do not have that permission\. 

## Step 2: Configure rotation and create a rotation function<a name="rotate-secrets_turn-on-for-db_step2"></a>

Rotation functions for Amazon RDS \(except Oracle\) and Amazon DocumentDB automatically use Secure Socket Layer \(SSL\) or Transport Layer Security \(TLS\) to connect to your database, if it is available\. Otherwise they use an unencrypted connection\.

**To turn on rotation for an Amazon RDS, Amazon DocumentDB, or Amazon Redshift secret**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the **Secrets** page, choose your secret\.

1. On the **Secret details** page, in the **Rotation configuration** section, choose **Edit rotation**\.

1. In the **Edit rotation configuration** dialog box, do the following:

   1. Turn on **Automatic rotation**\.

   1. Under **Rotation schedule**, enter your schedule in UTC time zone in either the **Schedule expression builder** or as a **Schedule expression**\. Secrets Manager stores your schedule as a `rate()` or `cron()` expression\. The rotation window automatically starts at midnight unless you specify a **Start time**\. For more information, see [Schedule expressions](rotate-secrets_schedule.md)\.

   1. \(Optional\) For **Window duration**, choose the length of the window during which you want Secrets Manager to rotate your secret, for example **3h** for a three hour window\. The window must not extend into the next rotation window\. If you don't specify **Window duration**, for a rotation schedule in hours, the window automatically closes after one hour\. For a rotation schedule in days, the window automatically closes at the end of the day\. 

   1. \(Optional\) Choose **Rotate immediately when the secret is stored** to rotate your secret when you save your changes\. If you clear the checkbox, then the first rotation will begin on the schedule you set\.

      If rotation fails, for example because Steps 3 and 4 are not yet completed, Secrets Manager retries the rotation process multiple times\.

   1. Under **Rotation function**, do one of the following:
      + Choose **Create a new Lambda function** and enter a name for your new function\. Secrets Manager adds `SecretsManager` to the beginning of the function name\. Secrets Manager creates the function based on the appropriate [template](reference_available-rotation-templates.md) and sets the necessary [permissions](rotating-secrets-required-permissions-function.md) for the Lambda execution role\.
      + Choose **Use an existing Lambda function** to reuse a rotation function you used for another secret\. The rotation functions listed under **Recommended VPC configurations** have the same VPC and security group as the database, which helps the function access the database\.

   1. For **Use separate credentials to rotate this secret**, do one of the following:
      + For the *single user rotation strategy*, choose **No**\.
      + For the *alternating users rotation strategy*, choose **Yes**\. Then choose the superuser secret that you created in Step 1\.

1. Choose **Save**\.

## Step 3: \(Optional\) Set an additional permissions condition on the rotation function<a name="rotate-secrets_turn-on-for-db_step3"></a>

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

## Step 4: Set up network access for the rotation function<a name="rotate-secrets_turn-on-for-db_step4"></a>

To be able to rotate a secret, the Lambda rotation function must be able to access both the secret and the database or service\.

**To access a secret**  
Your Lambda rotation function must be able to access a Secrets Manager endpoint\. If your Lambda function can access the internet, then you can use a public endpoint\. To find an endpoint, see [Secrets Manager endpoints ](asm_access.md#endpoints)\.  
If your Lambda function runs in a VPC that doesn't have internet access, we recommend you configure Secrets Manager service private endpoints within your VPC\. Your VPC can then intercept requests addressed to the public regional endpoint and redirect them to the private endpoint\. For more information, see [VPC endpoint](vpc-endpoint-overview.md)\.  
Alternatively, you can enable your Lambda function to access a Secrets Manager public endpoint by adding a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) or an [internet gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) to your VPC, which allows traffic from your VPC to reach the public endpoint\. This exposes your VPC to more risk because an IP address for the gateway can be attacked from the public Internet\.

**To access the database or service**  
If your database or service is running on an Amazon EC2 instance in a VPC, we recommend that you configure your Lambda function to run in the same VPC\. Then the rotation function can communicate directly with your service\. For more information, see [Configuring VPC access](https://docs.aws.amazon.com/lambda/latest/dg/configuration-vpc.html#vpc-configuring)\.  
To allow the Lambda function to access the database or service, you must make sure that the security groups attached to your Lambda rotation function allow outbound connections to the database or service\. You must also make sure that the security groups attached to your database or service allow inbound connections from the Lambda rotation function\. 

## Next steps<a name="rotate-secrets_turn-on-for-db_stepnext"></a>

See [Troubleshoot AWS Secrets Manager rotation](troubleshoot_rotation.md)\.