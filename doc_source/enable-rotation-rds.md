# Enabling Rotation for an RDS Database Secret<a name="enable-rotation-rds"></a>

You can enable rotation for a secret that has credentials for a [supported RDS database](rotating-secrets-rds.md#rds-supported-database-list) by using the [Secrets Manager console](#proc-enable-rotation-rds-console) or by using the AWS CLI or one of the AWS SDKs\.

**Warning**  
Enabling rotation causes the secret to rotate once immediately when you save the secret\. Before you enable rotation, ensure that all of your applications that use this secret's credentials are updated to retrieve the secret from AWS Secrets Manager\. The original credentials might no longer be usable after the initial rotation and any applications that you don't update will break as soon as the old credentials are no longer valid\.

**To enable and configure rotation for a supported RDS database secret**  
Follow the steps under one of the following tabs:

------
#### [ Using the console ]<a name="proc-enable-rotation-rds-console"></a>

**Minimum permissions**  
To enable and configure rotation in the console you must have the permissions provided by the following managed policies:  
`SecretsManagerReadWrite` \- provides all of the Secrets Manager, Lambda, and AWS CloudFormation permissions
`IAMFullAccess` \- provides the IAM permissions required to create a role and attach a permission policy to it

1. Sign\-in to the AWS Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose the name of the secret for which you want to enable rotation\.

1. In the **Configure automatic rotation** section, choose **Enable automatic rotation**\. This enables the other controls in this section\.

1. For **Select rotation interval**, choose one of the predefined values or choose **Custom** and then type the number of days you want between rotations\. If you are rotating your secret to meet compliance requirements, then we recommend that you set this value to at least 1 day less than the compliance\-mandated interval\. 
**Note**  
If you use the Secrets Manager provided Lambda function to alternate between two users \(the console uses this template if you choose the second "master secret" option in the next step\), then you should set your rotation period to one\-half of your compliance\-specified minimum interval\. This is because the old credentials are still available \(if not actively used\) for one additional rotation cycle\. The old credentials are fully invalidated only after the user is updated with a new password after the second rotation\. If you modify the rotation function to immediately invalidate the old credentials after the new secret becomes active then you can extend the rotation interval to your full compliance\-mandated minimum\. Leaving the old credentials active for one additional cycle with the `AWSPREVIOUS` staging label provides a "last known good" set of credentials that can be used for fast recovery\. If something happened to break the current credentials, you can simply move the `AWSCURRENT` staging label to the version that has the `AWSPREVIOUS` label and your customers should be able to access the resource again\. For more information, see [Rotating AWS Secrets Manager Secrets By Switching Between Two Existing Users](rotating-secrets-two-users.md)

1. Specify the secret with credentials that the rotation function can use to update the credentials on the protected database\.<a name="considerations-for-rotation"></a>
   + **Use this secret**: Choose this option if the credentials in this secret have permission in the database to change its own password\. Choosing this option causes Secrets Manager to implement a Lambda function that rotates secrets with a single user that gets its password changed with each rotation\.
**Note**  
This is the "lower availability" option, because sign\-in failures can occur between the moment when the old password is removed by the rotation and the moment when the updated password is made accessible as a new version of the secret\. Such a time window should be very short, on the order of a few seconds\. If you choose the **Use this secret** option, ensure that your client apps that sign in with the secret use an appropriate "backoff and retry with jitter" strategy in code\. A real failure should be reported only if signing in fails several times over a longer period of time\.
   + **Use a secret that I have previously stored in AWS Secrets Manager**: Choose this option if the credentials in the current secret do not have permissions to update the credentials, or you require high availability for the secret\. To choose this option, you must create a separate "master" secret with credentials that have permissions to update the current secret's credentials\. Then choose the master secret from the list\. Choosing this option causes Secrets Manager to implement a Lambda function that rotates secrets by creating a new user and password with each rotation and deprecating the old one\.
**Note**  
This is the "high availability" option because the old version of the secret continues to operate and service requests while the new version is prepared and tested\. The old version is not deleted until after the clients switch to the new version\. There is no down time while changing between versions\.  
This option requires the Lambda function to clone the permissions of old user and apply them to the new user in each rotation\.

1. Choose **Save** to store your changes and to trigger the initial rotation of the secret\.

1. If you chose to rotate your secret with a separate master secret, then you must manually grant your Lambda rotation function permission to access the master secret\. Follow these instructions:

   1. When rotation configuation completes, the following message appears at the top of the page:

      *Your secret *<secret name>* has been successfully stored and secret rotation is enabled\. To finish configuring rotation, you need to provide the *role* permissions to access the value of the secret *<ARN of your master secret>**\.

      You must manually modify the policy for the role to grant the rotation function GetSecretValue access to the master secret\. Secrets Manager cannot do this for you for security reasons\. Rotation of the secret will fail until you complete the following steps because it can't access the master secret\.

   1. Copy the ARN from the message to your clipboard\.

   1. Choose the link on the word "role" in the message to open the IAM console to the role details page for the role attached to the Lambda rotation function that Secrets Manager created for you\.

   1. On the **Permissions** tab, choose **Add inline policy**, and then set the following values:
      + For **Service**, choose **Secrets Manager**\.
      + For **Actions**, choose **GetSecretValue**\.
      + For **Resources**, choose **Add ARN** next to the **secret** resource type entry\.
      + In the **Add ARN\(s\)** dialog box, paste the ARN of the master secret that you copied previously\.

   1. Choose **Review policy**, and then choose **Create policy**\.
**Note**  
Alternatively to using the Visual Editor as described in the previous steps, you can paste the following statement into an existing or new policy:  

      ```
      { "Effect": "Allow", "Action": [ "secretsmanager:GetSecretValue" ], "Resource": "<ARN of the master secret>" }
      ```

   1. Return to the AWS Secrets Manager console\.

If there is not already an ARN for a Lambda function assigned to the secret, Secrets Manager creates the function, assigns all required permissions, and configures it to work with your database\. Secrets Manager counts down the number of days specified in the rotation interval and when it reaches zero, it rotates the secret again and resets the interval for the next cycle\. This continues until you disable rotation\.

------
#### [ Using the AWS CLI or AWS SDK operations ]

**Minimum permissions**  
To enable and configure rotation in the console you must have the permissions provided by the following managed policies:  
`SecretsManagerReadWrite` \- provides all of the Secrets Manager, Lambda, and AWS CloudFormation permissions
`IAMFullAccess` \- provides the IAM permissions required to create a role and attach a permission policy to it

You can use the following Secrets Manager commands to configure rotation for a existing secret for a supported RDS database:
+ **API/SDK:** [RotateSecret](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RotateSecret.html)
+ **CLI:** [RotateSecret](http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/rotate-secret.html)

You will also need to use commands from AWS CloudFormation and AWS Lambda\. Refer to the documentation for those services for more information about the commands used below\.

**To create a Lambda rotation function by using an AWS Serverless Application Repository template**  
The following is an example CLI session that performs the equivalent of the console\-based rotation configuration described in the **Using the console** tab\. You create the function using a AWS CloudFormation change set and then configure the resulting function with the required permissions\. Finally, you configure the secret with the ARN of the completed function and rotate once to test it\.

The following example uses the generic template and so uses the last ARN shown above\.

If your database or service resides in an Amazon VPC, then you must include the fourth command below that configures the function to communicate with that VPC\. If no VPC is involved, then you can skip that command\.

The first command sets up a AWS CloudFormation change set based on the Secrets Manager provided template\.

You use the `--application-id` parameter to specify which template to use\. The value is the ARN of the template\. For the list of AWS provided templates and their ARN, see [AWS Templates You Can Use to Create Lambda Rotation Functions ](reference_available-rotation-templates.md)\.

The templates also require additional parameters provided with `--parameter-overrides` as shown in the example that follows\. This parameter is required to pass two pieces of information to the template that affect how the rotation function is created:
+ **endpoint**: the URL of the service endpoint that you want the rotation function to query\. Typically, this will be `https://secretsmanager.region.amazonaws.com`
+ **functionname**: the name of the completed Lambda rotation function that is created by this process\.

```
$ aws serverlessrepo create-cloud-formation-change-set \
          --application-id arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRotationTemplate \
          --parameter-overrides '[{"Name":"endpoint","Value":"https://secretsmanager.region.amazonaws.com"},{"Name":"functionName","Value":"MyLambdaRotationFunction"}]' \
          --stack-name MyLambdaCreationStack
{
    "ApplicationId": "arn:aws:serverlessrepo:us-west-2:297356227824:applications/SecretsManagerRDSMySQLRotationSingleUser",
    "ChangeSetId": "arn:aws:cloudformation:us-west-2:123456789012:changeSet/EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE/EXAMPLE2-90ab-cdef-fedc-ba987EXAMPLE",
    "StackId": "arn:aws:cloudformation:us-west-2:123456789012:stack/aws-serverless-repository-MyLambdaCreationStack/EXAMPLE3-90ab-cdef-fedc-ba987EXAMPLE"
}
```

The next command runs the change set that you just created\. The change\-set\-name parameter comes from the `ChangeSetId` output of the previous command\. This command produces no output\.

```
$ aws cloudformation execute-change-set --change-set-name arn:aws:cloudformation:us-west-2:123456789012:changeSet/EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE/EXAMPLE2-90ab-cdef-fedc-ba987EXAMPLE
```

The following command grants the Secrets Manager service permission to call the function on your behalf\. The output shows the permission added to the role's trust policy\.

```
$ aws lambda add-permission \
          --function-name MyLambdaRotationFunction \
          --principal secretsmanager.amazonaws.com \
          --action lambda:InvokeFunction \
          --statement-id SecretsManagerAccess
{
    "Statement": "{\"Sid\":\"SecretsManagerAccess\",\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"secretsmanager.amazonaws.com\"},\"Action\":\"lambda:InvokeFunction\",\"Resource\":\"arn:aws:lambda:us-west-2:123456789012:function:MyLambdaRotationFunction\"}"
}
```

The following command is required only if your database is running in a VPC\. If it is not, skip this command\. Look up the VPC information for your RDS instance using either the RDS console or by using the `aws rds describe-instances` CLI command\. Then put that information in the following command and run it\.

```
$ aws lambda update-function-configuration \
          --function-name arn:aws:lambda:us-west-2:123456789012:function:MyLambdaRotationFunction \
          --vpc-config SubnetIds=<COMMA SEPARATED LIST OF VPC SUBNET IDS>,SecurityGroupIds=<COMMA SEPARATED LIST OF SECURITY GROUP IDs>
```

If you created a function using a template that requires a master secret, then you must also add the following statement to the function's role policy\. For complete instructions, see [Granting a Rotation Function Permission to Access a Separate Master Secret](auth-and-access_identity-based-policies.md#permissions-grant-rotation-role-access-to-master-secret)\.

```
        {
             "Action": "secretsmanager:GetSecretValue",
             "Resource": "arn:aws:secretsmanager:region:123456789012:secret:MyDatabaseMasterSecret",
             "Effect": "Allow"
        },
```

Finally, you can apply the rotation configuration to your secret and perform the initial rotation\.

```
$ aws secretsmanager rotate-secret \
          --secret-id production/MyAwesomeAppSecret \
          --rotation-lambda-arn arn:aws:lambda:us-west-2:123456789012:function:aws-serverless-repository-SecretsManagerRDSMySQLRo-10WGBDAXQ6ZEH \
          --rotation-rules AutomaticallyAfterDays=7
```

We recommend that even if you want to create your own Lambda rotation function for a supported RDS database, you should follow the preceding steps using the SecretsManagerRotationTemplate CloudFormation template, because it lets Secrets Manager set up most of the permissions and configuration settings for you\.

------