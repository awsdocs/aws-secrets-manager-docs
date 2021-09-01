# Rotating secrets for databases with built\-in rotation support<a name="rotating-secrets-built-in"></a>

AWS Secrets Manager has built\-in rotation support for:
+ Amazon RDS databases
+ Amazon DocumentDB databases
+ Amazon Redshift clusters

For these types of secrets, Secrets Manager provides a Lambda rotation function to automatically rotate your secret on a schedule\. For other types of secrets, see [Rotating AWS Secrets Manager secrets for other databases or services](rotating-secrets-other.md)\. 

When you turn on rotation for this type of secret, Secrets Manager creates a Lambda rotation function and an IAM role associated with the function\. See [Overview of the Lambda rotation function](rotating-secrets-lambda-function-overview.md)\.

To turn on rotation, you must have the permissions provided by the following managed policies:
+ `SecretsManagerReadWrite` – Provides Secrets Manager, Lambda, and AWS CloudFormation permissions\. See [Managed policies](reference_available-policies.md)\.
+ `IAMFullAccess` – Provides the IAM permissions required to create a role and attach a permission policy to it\. 

**To turn on rotation \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the **Secrets** page, choose your secret\.

1. On the **Secret details** page, in the **Rotation configuration** section, choose **Edit rotation**\.

1. In the **Edit rotation configuration** dialog box, do the following:

   1. Choose **Enable automatic rotation**\.

   1. For **Select rotation interval**, choose the number of days to keep the secret before rotating it\.

   1. Do one of the following:
      + Choose **Create a new Lambda function** and then enter the name for your new function\. Secrets Manager adds "SecretsManager" to the beginning of your function name\.
      + Choose **Use an existing Lambda function**\.

   1. Do one of the following:
      + Choose **Use this secret / Single\-user rotation** if the credentials are for a user who can change their database password\. See [Rotating AWS Secrets Manager secrets for one user with a single password](rotating-secrets-one-user-one-password.md)\.
      + Choose **Use a secret I previously stored / Multi\-user rotation** if the credentials are for a user who can't change their database password, or if you want to alternate between two sets of credentials for higher availability\. See [Rotating secrets by alternating between users](rotating-secrets-two-users.md)\. Then choose a secret that contains credentials for a superuser who can change the database password for the first user\. 

## AWS SDK and AWS CLI<a name="rotating-secrets-built-in_cli"></a>

To turn on rotation for a secret for a database with built\-in rotation support:
+ **API/SDK:** [RotateSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RotateSecret.html)
+ **AWS CLI:** [RotateSecret](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/rotate-secret.html)

You also use commands from AWS CloudFormation and AWS Lambda\. 

To turn on rotation for a secret, you create a Lambda function based on [Rotation function templates](reference_available-rotation-templates.md)\. 

**To turn on rotation \(AWS CLI\)**

1. Set up an AWS CloudFormation [change set](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-changesets-create.html) based on the [Rotation function templates](reference_available-rotation-templates.md) provided by Secrets Manager\.

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

1. Run the change set you just created\. The `change-set-name` parameter comes from the `ChangeSetId` output of the previous command\. This command produces no output\.

   ```
   $ aws cloudformation execute-change-set --change-set-name arn:aws:cloudformation:us-west-2:123456789012:changeSet/EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE/EXAMPLE2-90ab-cdef-fedc-ba987EXAMPLE
   ```

1. Grant Secrets Manager permission to call the function\. The output shows the permission added to the role trust policy\.

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

1. \(Optional\) If your database runs in a VPC, add the VPC information for your Amazon RDS DB instance\.

   ```
   $ aws lambda update-function-configuration \
             --function-name arn:aws:lambda:us-west-2:123456789012:function:MyLambdaRotationFunction \
             --vpc-config SubnetIds=<COMMA SEPARATED LIST OF VPC SUBNET IDS>,SecurityGroupIds=<COMMA SEPARATED LIST OF SECURITY GROUP IDs>
   ```

1. \(Optional\) If the VPC with your database and Lambda rotation function doesn't have internet access, then configure the VPC with a private service endpoint for Secrets Manager\. Then the rotation function can access Secrets Manager at an endpoint within the VPC\.

   ```
   $ aws ec2 create-vpc-endpoint --vpc-id <VPC ID> /
                                 --vpc-endpoint-type Interface /
                                 --service-name com.amazonaws.<region>.secretsmanager /
                                 --subnet-ids <COMMA SEPARATED LIST OF VPC SUBNET IDS> /
                                 --security-group-ids <COMMA SEPARATED LIST OF SECURITY GROUP IDs> /
                                 --private-dns-enabled
   ```

1. \(Optional\) If you created a function using a template that requires a superuser secret, grant permission to the rotation function to retrieve the secret value for the superuser secret\. For more information, see [Granting a rotation function permission to access a separate master secret](permissions-grant-rotation-role-access-to-master-secret.md)\.

   ```
           {
                "Action": "secretsmanager:GetSecretValue",
                "Resource": "arn:aws:secretsmanager:region:123456789012:secret:MyDatabaseMasterSecret",
                "Effect": "Allow"
           },
   ```

1. Apply the rotation configuration to your secret and perform the initial rotation\.

   ```
   $ aws secretsmanager rotate-secret \
             --secret-id production/MyAwesomeAppSecret \
             --rotation-lambda-arn arn:aws:lambda:us-west-2:123456789012:function:aws-serverless-repository-SecretsManagerRDSMySQLRo-10WGBDAXQ6ZEH \
             --rotation-rules AutomaticallyAfterDays=7
   ```

 