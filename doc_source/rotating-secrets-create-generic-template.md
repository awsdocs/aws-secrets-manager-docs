# Rotating AWS Secrets Manager Secrets for Other Databases or Services<a name="rotating-secrets-create-generic-template"></a>

If you create a secret for anything other than one of the supported Amazon RDS databases, then AWS Secrets Manager doesn't create the Lambda rotation function for you\. You must create and configure it, then provide the ARN of the completed function to the secret using either the AWS Secrets Manager console, the AWS CLI or one of the AWS SDKs\.

The procedure in this topic tells you how to create the Lambda function using a AWS CloudFormation change set that you create and run\. You then attach some permissions\. At that point you can edit the code to make the rotation function work the way you want it to\. Finally, you associate the completed function with your secret so that Secrets Manager calls the function every time rotation is triggered\.

You can specify the "generic" template that you must fully implement, or choose one of the templates that completely implement a rotation strategy for a certain database or service and use that as a starting point to customize the function to meet your needs\. 

**Minimum permissions**  
To run the commands that enable and configure rotation you must have the following permissions:  
`serverlessrepo:CreateCloudFormationChangeSet` \- to create the AWS CloudFormation change set that configures and creates the Lambda rotation function
`cloudformation:ExecuteChangeSet` \- to run the AWS CloudFormation change set that creates and configures the Lambda rotation function
`lambda:AddPermission` \- to add the required permissions to the Lambda rotation function after it is created
`lambda:InvokeFunction` \- to attach the rotation function to the secret
`lambda:UpdateFunctionConfiguration `\- to allow the console to update the VPC configuration of the Lambda function so it can communicate with a database or service that resides in a VPC
`secretsmanager:RotateSecret` \- to configure and trigger the initial rotation
You can grant all of these permissions to an IAM user or role by attaching the [SecretsManagerReadWrite](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/SecretsManagerReadWrite) AWS managed policy\. 

The commands shown below apply the generic `SecretsManagerRotationTemplate` to your Lambda function\. This template comes from the AWS Serverless Application Repository and is used by AWS CloudFormation to automate most of the steps for you\. The complete set of templates and the ARN you must specify can be found at [AWS Templates You Can Use to Create Lambda Rotation Functions ](reference_available-rotation-templates.md)\. 

The ARN of the generic template is as follows and must be entered exactly as shown:

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRotationTemplate
```

If the database or service your credentials are for resides in an Amazon VPC, then you must include the command in step 5 that configures the function to communicate with that VPC\. If no VPC is involved, then you can skip that command\.

**To create a Lambda rotation function as a generic template to customize**

1. The first command sets up a AWS CloudFormation change set based on the Secrets Manager provided template\. You provide two parameters to the template: the Secrets Manager endpoint URL and the name that you want to give to the Lambda rotation function that the template produces\.

   ```
   $ aws serverlessrepo create-cloud-formation-change-set \
             --application-id arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRotationTemplate \
             --stack-name MyLambdaCreationStack \
             --parameter-overrides '[{"Name":"endpoint","Value":"https://secretsmanager.region.amazonaws.com"},{"Name":"functionName","Value":"MySecretsManagerRotationFuncion"}]'
   {
       "ApplicationId": "arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSMySQLRotationSingleUser",
       "ChangeSetId": "arn:aws:cloudformation:region:123456789012:changeSet/EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE/EXAMPLE2-90ab-cdef-fedc-ba987EXAMPLE",
       "StackId": "arn:aws:cloudformation:region:123456789012:stack/aws-serverless-repository-MyLambdaCreationStack/EXAMPLE3-90ab-cdef-fedc-ba987EXAMPLE"
   }
   ```

1. The next command runs the change set that you just created\. The change\-set\-name parameter comes from the `ChangeSetId` output of the previous command\. This command produces no output:

   ```
   $ aws cloudformation execute-change-set --change-set-name arn:aws:cloudformation:region:123456789012:changeSet/EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE/EXAMPLE2-90ab-cdef-fedc-ba987EXAMPLE
   ```

1. Next, you must find the name of the Lambda function that the previous command just created for you\. The function name begins with `aws-serverless-repository-` followed by the first 24 characters of the template's name followed by a dash and a random string of characters\.

   ```
   $ aws lambda list-functions
     {
       ...
       "FunctionName": "MySecretsManagerRotationFuncion",
       "FunctionArn": "arn:aws:lambda:us-west-2:123456789012:function:MySecretsManagerRotationFuncion",
       ...
     }
   ```

1. The following command grants the Secrets Manager service permission to call the function on your behalf\.

   ```
   $ aws lambda add-permission \
             --function-name MySecretsManagerRotationFuncion \
             --principal secretsmanager.amazonaws.com \
             --action lambda:InvokeFunction \
             --statement-id SecretsManagerAccess
   {
       "Statement": "{\"Sid\":\"SecretsManagerAccess\",\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"secretsmanager.amazonaws.com\"},\"Action\":\"lambda:InvokeFunction\",\"Resource\":\"arn:aws:lambda:us-west-2:123456789012:function:aws-serverless-repository-SecretsManagerRDSMySQLRo-10WGBDAXQ6ZEH\"}"
   }
   ```

1. The following commands is required only if your database is running in a VPC\. If it is not, skip this command\. Look up the VPC information for your RDS instance using either the RDS console or by using the aws rds describe\-instances CLI command\. Then put that information in the following command and run it\.

   ```
   $ aws lambda update-function-configuration \
             --function-name arn:aws:lambda:us-west-2:123456789012:function:MySecretsManagerRotationFuncion \
             --vpc-config SubnetIds=<COMMA SEPARATED LIST OF VPC SUBNET IDS>,SecurityGroupIds=<COMMA SEPARATED LIST OF SECURITY GROUP IDs> \
   ```

1. At this point your Lambda rotation function is ready for you to enter the code\.

   Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Customize the code to implement your chosen rotation scenario\. For details, see [Understanding and Customizing Your Lambda Rotation Function](rotating-secrets-lambda-function-customizing.md)\.

1. Finally, you can apply the rotation configuration to your secret and perform the initial rotation\. Specify the number of days between successive rotations with the `--rotation-rules` parameter, setting `AutomaticallyAfterDays` to the number of days you want\.

   ```
   $ aws secretsmanager rotate-secret \
             --secret-id production/MyAwesomeAppSecret \
             --rotation-lambda-arn arn:aws:lambda:us-west-2:123456789012:function:MySecretsManagerRotationFuncion \
             --rotation-rules AutomaticallyAfterDays=7
   ```

The secret rotates once immediately and then begins to rotate as frequently as you specified\.