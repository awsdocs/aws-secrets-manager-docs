# Enabling Rotation for an Amazon DocumentDB Secret<a name="enable-rotation-documentdb"></a>

You can enable rotation for a secret with credentials for a Amazon DocumentDB database by using the [AWS Secrets Manager console](#proc-enable-rotation-documentdb-console), the AWS CLI, or one of the AWS SDKs\.

**Warning**  
Enabling rotation causes the secret to rotate once immediately when you save the secret\. Before you enable rotation, ensure you update all of your applications using the secret credentials to retrieve the secret from Secrets Manager\. The original credentials might not be usable after the initial rotation\. If you don't update all of your applications, you might encounter errors as the old credentials become invalid\. 

**Prerequisites: Network Requirements to Enable Rotation**  
To successfully enable rotation, configure your network environment correctly\.
+ **The Lambda function must be able to communicate with the database\.** If you run your DocumentDB database instance in a [VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Introduction.html), we recommend you configure your Lambda function to run in the same VPC\. This enables direct connectivity between the rotation function and your service\. To configure this, on the Lambda function details page, scroll down to the **Network** section and choose the **VPC** from the drop\-down list to match your instance\. You must also make sure the EC2 security groups attached to your instance enable communication between the instance and Lambda\.
+ **The Lambda function must be able to communicate with the Secrets Manager service endpoint\.** If your Lambda rotation function can access the Internet, either because you haven't configured the function to run in a VPC, or because the VPC has an [attached NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html), then you can use [any of the available public endpoints for Secrets Manager](https://docs.aws.amazon.com/general/latest/gr/rande.html#asm_region)\. Alternatively, if you configure your Lambda function to run in a VPC without Internet access, then you can [configure the VPC with a private Secrets Manager service endpoint](rotation-network-rqmts.md)\.

**Enabling and configuring rotation for a Amazon DocumentDB secret**  
Follow the steps under one of the following tabs:

------
#### [ Using the AWS Management Console ]<a name="proc-enable-rotation-documentdb-console"></a>

**Minimum permissions**  
To enable and configure rotation in the console, you must have the permissions provided by the following managed policies:  
`SecretsManagerReadWrite` – Provides all of the Secrets Manager, Lambda, and AWS CloudFormation permissions\.
`IAMFullAccess` – Provides the IAM permissions required to create a role and attach a permission policy to it\.

1. Sign in to the AWS Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose the name of the secret to enable rotation\.

1. In the **Configure automatic rotation** section, choose **Enable automatic rotation**\. This enables the other controls in this section\.

1. For **Select rotation interval**, choose one of the predefined values—or choose **Custom**, and then type the number of days you want between rotations\. If you rotate your secret to meet compliance requirements, then we recommend you set this value to at least 1 day less than the compliance\-mandated interval\. 

   Secrets Manager schedules the next rotation when the previous one is complete\. Secrets Manager schedules the date by adding the rotation interval \(number of days\) to the actual date of the last rotation\. The service chooses the hour within that 24\-hour date window randomly\. The minute is also chosen somewhat randomly, but is weighted towards the top of the hour and influenced by a variety of factors that help distribute load\.
**Note**  
If you use the Lambda function provided by Secrets Manager to alternate between two users, the console uses this template if you choose the second *master secret* option in the next step, then you should set your rotation period to one\-half of your compliance\-specified minimum interval\. Secrets Manager retains the old credentials, if not actively used, for one additional rotation cycle\. Then Secrets Manager invalidates the old credentials after updating the user with a new password after the second rotation\.   
If you modify the rotation function to immediately invalidate the old credentials after the new secret becomes active, then you can extend the rotation interval to your full compliance\-mandated minimum\. Leaving the old credentials active for one additional cycle with the `AWSPREVIOUS` staging label provides a *last known good* set of credentials you can use for fast recovery\. If something happens to break the current credentials, you can simply move the `AWSCURRENT` staging label to the version with the `AWSPREVIOUS` label\. Then you can access the resource again\. For more information, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.

1. Choose one of the following options:
   + **You want to create a new Lambda rotation function**

     1. Choose **Create a new Lambda function to perform rotation**\.

     1. For **Lambda function name**, enter the name you want to assign to the Lambda function that Secrets Manager creates for you\.

     1. Specify the secret with credentials the rotation function can use\. The user must have permissions to update the user name and password on the protected database\.<a name="considerations-for-rotation"></a>
        + **Use this secret**: Choose this option if the credentials in this secret have permission in the database to change their password\. Choosing this option causes Secrets Manager to create a Lambda function that rotates secrets with a single user whose password changes with each rotation\.
**Note**  
Secrets Manager provides this option as a lower availability option\. Sign\-in failures can occur between the moment when rotation removes the old password and the moment when the updated password becomes accessible as the new version of the secret\. This window of time may be very short—on the order of a second or less\.  
If you choose this option, make sure your client applications implement an appropriate ["backoff and retry with jitter" strategy](http://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/) in their code\. The applications should generate an error only if sign\-in fails several times over a longer period of time\.
        + **Use a secret that I have previously stored in AWS Secrets Manager**: Choose this option if the credentials in the current secret don't allow update the permissions , or if you require high availability for the secret\. To choose this option, you must create a separate "master" secret with credentials and permissions to update this secret credentials\. Then choose the master secret from the list\. Choosing this option causes Secrets Manager to create a Lambda function that rotates secrets by creating a new user and password with each rotation, and deprecating the old user\.
**Note**  
Secrets Manager provides this option as a high availability option because the old version of the secret continues to operate and handle service requests while preparing and testing the new version\. Secrets Manager doesn't deprecate the old version until the next rotation\. No downtime occurs while changing between versions\.  
This option requires the Lambda function to clone the permissions of the original user and apply them to the new user\. The function then alternates between the two users with each rotation\.  
If you need to change the permissions granted to the users, ensure you change permissions for both users\.
   + **You want to use a Lambda function you already created for another secret**

     1. Choose **Use an existing Lambda function to perform rotation**\.

     1. Choose the Lambda function from the drop\-down list\.

     1. Specify the type of rotation function :
        + **Single\-user rotation**: A rotation function for a secret that stores credentials for a user with permissions to change their password\. You create this type of function when you choose the option **Use this secret** when you create a function\.
        + **Multi\-user rotation**: A rotation function for a secret that stores credentials for a user unable to change their password\. The function requires a separate master secret that stores credentials for a user with permission to change the credentials for this secret user\. You create this type of function when you choose the option **Use a secret that I have previously stored in AWS Secrets Manager** \.

     1. If you specified the second "master secret" option, then you must also choose the secret that can provide the master user credentials\. The credentials in the master secret must have permission to update the credentials stored in this secret\.

1. Choose **Save** to store your changes and to trigger the initial rotation of the secret\.

1. If you chose to rotate your secret with a separate master secret, then you must manually grant your Lambda rotation function permission to access the master secret\. Follow these instructions:

   1. When rotation configuration completes, the following message might appear at the top of the page:

      *Your secret *<secret name>* has been successfully stored and secret rotation is enabled\. To finish configuring rotation, you need to provide the *role* permissions to access the value of the secret *<Amazon Resource Name \(ARN\) of your master secret>**\.

      If this appears, then you must manually modify the policy for the role to grant the rotation function `secretsmanager:GetSecretValue` permission to access the master secret\. Secrets Manager can't do this for you for security reasons\. If Secrets Manager can't access the master secret, then Rotation of the secret fails until you complete the following steps\.

   1. Copy the Amazon Resource Name \(ARN\) from the message to your clipboard\.

   1. Choose the link on the word "role" in the message\. This opens the IAM console to the role details page for the role attached to the Lambda rotation function Secrets Manager created for you\.

   1. On the **Permissions** tab, choose **Add inline policy**, and then set the following values:
      + For **Service**, choose **Secrets Manager**\.
      + For **Actions**, choose **GetSecretValue**\.
      + For **Resources**, choose **Add ARN** next to the **secret** resource type entry\.
      + In the **Add ARN\(s\)** dialog box, paste the ARN of the master secret you copied previously\.

   1. Choose **Review policy**, and then choose **Create policy**\.
**Note**  
As an alternative to using the Visual Editor as described in the previous steps, you can paste the following statement into an existing or new policy:  

      ```
      {
          "Effect": "Allow",
          "Action": "secretsmanager:GetSecretValue",
          "Resource": "<ARN of the master secret>" 
      }
      ```

   1. Return to the AWS Secrets Manager console\.

If you do not have an ARN for a Lambda function assigned to the secret, Secrets Manager creates the function, assigns all required permissions, and configures the function to work with your database\. Secrets Manager counts down the number of days specified in the rotation interval\. When the count reaches zero, Secrets Manager rotates the secret again and resets the interval for the next cycle\. This continues until you disable rotation\.

------
#### [ Using the AWS CLI or SDK Operations ]

**Minimum permissions**  
To enable and configure rotation in the console, you must have the permissions provided by the following managed policies:  
`SecretsManagerReadWrite` – Provides all of the Secrets Manager, Lambda, and AWS CloudFormation permissions\.
`IAMFullAccess` – Provides the IAM permissions required to create a role and attach a permission policy to it\.

You can use the following Secrets Manager commands to configure rotation for an existing secret for a supported Amazon RDS database:
+ **API/SDK:** [RotateSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RotateSecret.html)
+ **AWS CLI:** [RotateSecret](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/rotate-secret.html)

You also need to use commands from AWS CloudFormation and AWS Lambda\. For more information about the following commands , see the documentation for those services\.

**Important**  
The rotation function determines the exact format of the secret value you must use in your secret\. For the details of each rotation function requirement for the secret value, see the **Expected SecretString Value** entry under the relevant rotation function at [AWS Templates You Can Use to Create Lambda Rotation Functions ](reference_available-rotation-templates.md)\.

**Creating a Lambda rotation function by using an AWS Serverless Application Repository template**  
 An example follows of a AWS CLI session that performs the equivalent of the console\-based rotation configuration described in the **Using the AWS Management Console** tab\. You create the function by using an AWS CloudFormation change set\. Then you configure the resulting function with the required permissions\. Finally, you configure the secret with the ARN of the completed function, and rotate once to test it\.

The following example uses the generic template, and uses the last ARN shown earlier\.

The first command sets up an AWS CloudFormation change set based on the template provided by Secrets Manager\.

If your database or service resides in a VPC provided by Amazon VPC, then you must include the fourth of the following commands to configure the function to communicate with that VPC\. If you don't have VPC, then you can skip the command\.

Also, if your VPC doesn't have access to the public internet, then you must configure your VPC with a private service endpoint for Secrets Manager\. The fifth of the following commands does that\.

The templates also require additional parameters provided with `--parameter-overrides`, as shown in the example that follows\. This parameter requires Secrets Manager to send two pieces of information as Name and Value pairs to the template that affect the creation of the rotation function:
+ **endpoint** – The URL of the service endpoint you want the rotation function to query\. Typically, this is `https://secretsmanager.region.amazonaws.com`\.
+ **functionname** – The name of the completed Lambda rotation function created by this process\.

```
$ aws serverlessrepo create-cloud-formation-change-set \
          --application-id &region-arn;serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRotationTemplate \
          --parameter-overrides '[{"Name":"endpoint","Value":"https://secretsmanager.region.amazonaws.com"},{"Name":"functionName","Value":"MyLambdaRotationFunction"}]' \
          --stack-name MyLambdaCreationStack
{
    "ApplicationId": "&region-arn;serverlessrepo:us-west-2:297356227824:applications/SecretsManagerRDSMySQLRotationSingleUser",
    "ChangeSetId": "&region-arn;cloudformation:us-west-2:123456789012:changeSet/EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE/EXAMPLE2-90ab-cdef-fedc-ba987EXAMPLE",
    "StackId": "&region-arn;cloudformation:us-west-2:123456789012:stack/aws-serverless-repository-MyLambdaCreationStack/EXAMPLE3-90ab-cdef-fedc-ba987EXAMPLE"
}
```

The next command runs the change set you just created\. The `change-set-name` parameter comes from the `ChangeSetId` output of the previous command\. This command produces no output\.

```
$ aws cloudformation execute-change-set --change-set-name &region-arn;cloudformation:us-west-2:123456789012:changeSet/EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE/EXAMPLE2-90ab-cdef-fedc-ba987EXAMPLE
```

The following command grants the Secrets Manager service permission to call the function for you\. The output shows the permission added to the role trust policy\.

```
$ aws lambda add-permission \
          --function-name MyLambdaRotationFunction \
          --principal secretsmanager.amazonaws.com \
          --action lambda:InvokeFunction \
          --statement-id SecretsManagerAccess
{
    "Statement": "{\"Sid\":\"SecretsManagerAccess\",\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"secretsmanager.amazonaws.com\"},\"Action\":\"lambda:InvokeFunction\",\"Resource\":\"&region-arn;lambda:us-west-2:123456789012:function:MyLambdaRotationFunction\"}"
}
```

If you run your database in a VPC environment, Secrets Manager requires the following command\. \. If not, skip this command\. Find the VPC information for your Amazon RDS DB instance by using either the Amazon RDS console, or by using the `aws rds describe-instances` CLI command\. Then add the information in the following command and run it\.

```
$ aws lambda update-function-configuration \
          --function-name &region-arn;lambda:us-west-2:123456789012:function:MyLambdaRotationFunction \
          --vpc-config SubnetIds=<COMMA SEPARATED LIST OF VPC SUBNET IDS>,SecurityGroupIds=<COMMA SEPARATED LIST OF SECURITY GROUP IDs>
```

If the VPC with your database instance and Lambda rotation function doesn't have Internet access, then you must configure the VPC with a private service endpoint for Secrets Manager\. This enables the rotation function to access Secrets Manager at an endpoint within the VPC\.

```
$ aws ec2 create-vpc-endpoint --vpc-id <VPC ID> /
                              --vpc-endpoint-type Interface /
                              --service-name com.amazonaws.<region>.secretsmanager /
                              --subnet-ids <COMMA SEPARATED LIST OF VPC SUBNET IDS> /
                              --security-group-ids <COMMA SEPARATED LIST OF SECURITY GROUP IDs> /
                              --private-dns-enabled
```

If you created a function using a template that requires a master secret, then you must also add the following statement to the function role policy\. This grants permission to the rotation function to retrieve the secret value for the master secret\. For complete instructions, see [Granting a Rotation Function Permission to Access a Separate Master Secret](auth-and-access_identity-based-policies.md#permissions-grant-rotation-role-access-to-master-secret)\.

```
        {
             "Action": "secretsmanager:GetSecretValue",
             "Resource": "&region-arn;secretsmanager:region:123456789012:secret:MyDatabaseMasterSecret",
             "Effect": "Allow"
        },
```

Finally, you can apply the rotation configuration to your secret and perform the initial rotation\.

```
$ aws secretsmanager rotate-secret \
          --secret-id production/MyAwesomeAppSecret \
          --rotation-lambda-arn &region-arn;lambda:us-west-2:123456789012:function:aws-serverless-repository-SecretsManagerRDSMySQLRo-10WGBDAXQ6ZEH \
          --rotation-rules AutomaticallyAfterDays=7
```

We recommend if you want to create your own Lambda rotation function, you should follow the preceding steps that use the SecretsManagerRotationTemplate AWS CloudFormation template\. Secrets Manager sets up most of the permissions and configuration settings for you\.

------