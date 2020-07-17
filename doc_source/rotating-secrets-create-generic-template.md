# Rotating AWS Secrets Manager Secrets for Other Databases or Services<a name="rotating-secrets-create-generic-template"></a>

If you create a secret for another application besides one of the supported Amazon RDS databases, then AWS Secrets Manager doesn't create the Lambda rotation function for you\. You must create and configure it, and then provide the Amazon Resource Name \(ARN\) of the completed function to the secret\. You do this by using the Secrets Manager console, the AWS CLI, or one of the AWS SDKs\.

This topic describes creating the Lambda function by using an AWS CloudFormation change set you create and run\. You then attach permissions\. At that point, you can edit the code to make the rotation function work the way you want it to\. Finally, you associate the completed function with your secret so that Secrets Manager calls the function every time rotation triggers\.

You can specify the "generic" template you must fully implement\. Or you can choose one of the templates that completely implement a rotation strategy for a certain database or service, and use it as a starting point to customize the function to meet your needs\. 

**Minimum permissions**  
To run the commands that enable and configure rotation, you must have the following permissions:  
`serverlessrepo:CreateCloudFormationChangeSet` – To create the AWS CloudFormation change set that configures and creates the Lambda rotation function\.
`cloudformation:ExecuteChangeSet` – To run the AWS CloudFormation change set that creates and configures the Lambda rotation function\.
`lambda:AddPermission` – Add the required permissions to the Lambda rotation function after you create\. it
`lambda:InvokeFunction` – Attach the rotation function to the secret\.
`lambda:UpdateFunctionConfiguration` – Allow the console to update the VPC configuration of the Lambda function so it can communicate with a database or service that resides in a VPC\.
`secretsmanager:RotateSecret` – Configure and trigger the initial rotation\.
You can grant all of these permissions to an IAM user or role by attaching the [SecretsManagerReadWrite](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/SecretsManagerReadWrite) AWS managed policy\. 

The following commands apply the generic `SecretsManagerRotationTemplate` to your Lambda function\. This template comes from the AWS Serverless Application Repository, and used by AWS CloudFormation to automate most of the steps for you\. For the complete set of templates and the ARN that you must specify, see [AWS Templates You Can Use to Create Lambda Rotation Functions ](reference_available-rotation-templates.md)\. 

Use the ARN of the generic template and enter it exactly as shown:

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRotationTemplate
```

If the database or service using your credentials resides in a VPC provided by Amazon VPC, then you must include the command in Step 5\. This command configures the function to communicate with that VPC\. If you do not have a VPC, then you can skip the command\.

**Creating a Lambda rotation function as a generic template for customization**

1. The first command sets up an AWS CloudFormation change set based on the template provided by Secrets Manager\. You provide two parameters to the template: the Secrets Manager endpoint URL and the name for the Lambda rotation function produced by the template\.

   ```
   $ aws serverlessrepo create-cloud-formation-change-set \
             --application-id arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRotationTemplate \
             --stack-name MyLambdaCreationStack \
             --parameter-overrides '[{"Name":"endpoint","Value":"https://secretsmanager.region.amazonaws.com"},{"Name":"functionName","Value":"MySecretsManagerRotationFunction"}]' --capabilities CAPABILITY_IAM CAPABILITY_RESOURCE_POLICY
   {
       "ApplicationId": "arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSMySQLRotationSingleUser",
       "ChangeSetId": "arn:aws:cloudformation:region:123456789012:changeSet/EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE/EXAMPLE2-90ab-cdef-fedc-ba987EXAMPLE",
       "StackId": "arn:aws:cloudformation:region:123456789012:stack/aws-serverless-repository-MyLambdaCreationStack/EXAMPLE3-90ab-cdef-fedc-ba987EXAMPLE"
   }
   ```

1. The next command runs the change set you just created\. The `change-set-name` parameter comes from the `ChangeSetId` output of the previous command\. This command produces no output:

   ```
   $ aws cloudformation execute-change-set --change-set-name arn:aws:cloudformation:region:123456789012:changeSet/EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE/EXAMPLE2-90ab-cdef-fedc-ba987EXAMPLE
   ```

1. Next you must locate the name of the Lambda function the previous command created for you\. 

   ```
   $ aws lambda list-functions
     {
       ...
       "FunctionName": "MySecretsManagerRotationFunction",
       "FunctionArn": "arn:aws:lambda:us-west-2:123456789012:function:MySecretsManagerRotationFunction",
       ...
     }
   ```

1. The following command grants Secrets Manager permission to call the function \.

   ```
   $ aws lambda add-permission \
             --function-name MySecretsManagerRotationFunction \
             --principal secretsmanager.amazonaws.com \
             --action lambda:InvokeFunction \
             --statement-id SecretsManagerAccess
   {
       "Statement": "{\"Sid\":\"SecretsManagerAccess\",\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"secretsmanager.amazonaws.com\"},\"Action\":\"lambda:InvokeFunction\",\"Resource\":\"arn:aws:lambda:us-west-2:123456789012:function:aws-serverless-repository-SecretsManagerRDSMySQLRo-10WGBDAXQ6ZEH\"}"
   }
   ```

1. If you run a database in a VPC, you need the following command\. If you don't have a VPC, skip this command\. This command configures the Lambda rotation function to run in the VPC running your Amazon RDS DB instance Look up the VPC information for your Amazon RDS DB instance by using either the Amazon RDS console, or by using the `aws rds describe-instances` CLI command\. Then add the information in the following command and execute it\.

   ```
   $ aws lambda update-function-configuration \
             --function-name arn:aws:lambda:us-west-2:123456789012:function:MySecretsManagerRotationFunction \
             --vpc-config SubnetIds=<COMMA SEPARATED LIST OF VPC SUBNET IDS>,SecurityGroupIds=<COMMA SEPARATED LIST OF SECURITY GROUP IDs> \
   ```

1. If the VPC with your database instance and Lambda rotation function without an internet access, then you must configure the VPC with a private service endpoint for Secrets Manager\. This enables the rotation function to access Secrets Manager at an endpoint within the VPC\.

   ```
   $ aws ec2 create-vpc-endpoint --vpc-id <VPC ID> \
                                 --vpc-endpoint-type Interface \
                                 --service-name com.amazonaws.<region>.secretsmanager \
                                 --subnet-ids <COMMA SEPARATED LIST OF VPC SUBNET IDS> \
                                 --security-group-ids <COMMA SEPARATED LIST OF SECURITY GROUP IDs> \
                                 --private-dns-enabled
   ```

1. At this point, you can enter your code into your Lambda rotation function \.

   Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Customize the code to implement your chosen rotation scenario\. For details, see [Understanding and Customizing Your Lambda Rotation Function](rotating-secrets-lambda-function-customizing.md)\.

1. Finally, you can apply the rotation configuration to your secret and perform the initial rotation\. Specify the number of days between successive rotations with the `--rotation-rules` parameter, and set `AutomaticallyAfterDays` to the desired number of days\.

   ```
   $ aws secretsmanager rotate-secret \
             --secret-id production/MyAwesomeAppSecret \
             --rotation-lambda-arn arn:aws:lambda:us-west-2:123456789012:function:MySecretsManagerRotationFunction \
             --rotation-rules AutomaticallyAfterDays=7
   ```

The secret rotates once immediately, and then begins to rotate as frequently as you specified\.