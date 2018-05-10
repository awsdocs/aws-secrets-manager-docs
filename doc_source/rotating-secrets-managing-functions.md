# Deleting Lambda Rotation Functions That You No Longer Need<a name="rotating-secrets-managing-functions"></a>

After you create a rotation function for a secret, the time will come when that secret is no longer needed\. [Deleting the secret](manage_delete-restore-secret.md) is an obvious step\. However, you might also want to consider removing the Lambda rotation function that rotates the secret\. If you share the rotation function among several secrets, then of course you don't want to delete the function until you delete the last secret rotated by the function\.

If you create the function as described in this guide using AWS Serverless Application Repository template then you don't simply delete the function\. Secrets Manager created the function as part of a AWS CloudFormation 'stack'\. Deleting the stack deletes everything that the stack created\. In this case, it is both the Lambda function and the IAM role that grants permissions to the function\. You must perform the following steps to delete everything cleanly\.

To delete a rotation function that you created with an AWS Serverless Application Repository template

------
#### [ Using the console ]

**Minimum permissions**  
To delete a Lambda rotation function that you created using one of the AWS Serverless Application Repository templates, you must have permissions required to navigate to your stack and delete all of its created components:  
cloudformation:ListStacks
cloudformation:DescribeStack
cloudformation:ListStackResources
cloudformation:DeleteStack
lambda:ListFunctions
lambda:GetFunction
lambda:DeleteFunction
iam:ListRoles
iam:DetachRolePolicy
iam:DeleteRolePolicy
iam:DeleteRole
cloudformation:DeleteStack
You can grant all of these by attaching the following AWS managed policies:  
[SecretsManagerReadWrite](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/SecretsManagerReadWrite)
[IAMFullAccess](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/IAMFullAccess)

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Navigate to the list of functions and choose the name of the function that you want to delete\.

1. On the function's details page, a banner at the top says **This function belongs to the CloudFormation stack **aws\-serverless\-repository\-SecretsManager*<rotation\_template\_name>**<unique\_guid>***\. Visit the CloudFormation console to manage this stack\.** Choose the **CloudFormation console** link to open the AWS CloudFormation console on that stack's details page\.

1. If you added any inline permission policies to the IAM role \(instead of editing the existing inline policies\), or if you attached any additional managed policies then you must delete or detach those policies before AWS CloudFormation can delete the stack:

   1. Expand the **Resources** section for the stack, and then choose the **Physical ID** vale for the row with the **Type** set to **AWS::IAM::Role**\. This opens the IAM console in a separate tab\.

   1. Examine the rows with **Inline policy** as the **Policy type**\. You should see the AWSLambdaBasicExecutionRole AWS managed function attached, as well as one, possibly two inline policies name **SecretsManager*<template name>*Policy0** and **SecretsManager*<template name>*Policy1**\. If there are any policies other than those, you should manually delete or detach them\. If you don't the request to delete the stack in the following steps can fail\.

   1. Return to the **Stack Detail** page of the AWS CloudFormation console\.

1. Choose **Other Actions**, then choose **Delete Stack**

1. On the **Delete Stack** confirmation dialog, choose **Yes, Delete**\.

   The **Status** changes to **DELETE\_IN\_PROGRESS**, and if successful, eventually to **DELETE\_COMPLETE**\.

1. When you return to the list of stacks, the one you just delete is no longer present\.

------
#### [ Using the AWS CLI or AWS SDK operations ]

**Minimum permissions**  
To delete a Lambda rotation function that you created using one of the AWS Serverless Application Repository templates, you must have permissions required to perform each of the CLI or equivalent API operations listed in the following steps\. You can grant all of these by attaching the following AWS managed policies:  
[SecretsManagerReadWrite](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/SecretsManagerReadWrite)
[IAMFullAccess](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/IAMFullAccess)

1. Start a command prompt where you can run the AWS CLI commands\.

1. To determine which AWS CloudFormation stack contains a specific function, run the following command with the function name passed as the `--physical-resource-id` parameter\. This results in a list of resources associated with the stack that owns the specified function\.

   ```
   $ aws cloudformation describe-stack-resources --physical-resource-id MyLambdaRotationFunction
   {{
       "StackResources": [
           {
               "StackName": "aws-serverless-repository-MyLambdaCreationStack",
               "StackId": "arn:aws:cloudformation:us-east-1:123456789012:stack/aws-serverless-repository-MyLambdaCreationStack/EXAMPLE2-90ab-cdef-fedc-ba987EXAMPLE",
               "LogicalResourceId": "SecretsManagerRotationTemplate",
               "PhysicalResourceId": "MySecretsManagerRotationFunction",
               "ResourceType": "AWS::Lambda::Function",
               "Timestamp": "2018-04-27T18:03:05.490Z",
               "ResourceStatus": "CREATE_COMPLETE"
           },
           {
               "StackName": "aws-serverless-repository-MyLambdaCreationStack",
               "StackId": "arn:aws:cloudformation:us-east-1:123456789012:stack/aws-serverless-repository-MyLambdaCreationStack/EXAMPLE2-90ab-cdef-fedc-ba987EXAMPLE",
               "LogicalResourceId": "SecretsManagerRotationTemplateRole",
               "PhysicalResourceId": "aws-serverless-repository-SecretsManagerRotationTe-<random-chars>",
               "ResourceType": "AWS::IAM::Role",
               "Timestamp": "2018-04-27T18:03:00.623Z",
               "ResourceStatus": "CREATE_COMPLETE"
           }
       ]
   }
   ```

1. We're initially interested in the `StackId` response value and the `PhysicalResourceId` response value that is associated with the "ResourceType" :"AWS::IAM::Role"\. This is the name of the IAM role that has permissions to invoke the function\. If you added any inline permission policies to the IAM role \(instead of editing the existing inline policies\), or if you attached any additional managed policies then you must delete or detach those policies before AWS CloudFormation can delete the stack\.

   To determine if there are any embedded inline policies, run the following command\. 

   ```
   $ aws iam list-role-policies --role-name <role-name-from-PhysicalResourceId-on-previous-command>
   {
       "PolicyNames": [
           "SecretsManagerRotationTemplateRolePolicy0",
           "SecretsManagerRotationTemplateRolePolicy1"
       ]
   }
   ```

   The inline policies that are named as shown here are expected and you don't need to do anything with them\.

1. If there are any policies listed other than those two, you must delete them from the role before you can proceed\.

   ```
   $ aws iam delete-role-policy --role-name <role-name-from-PhysicalResourceId-on-previous-command> /
          --policy-name <policy-name-from-previous-command>
   ```

1. Now check to see if there are any managed policies attached to the role:

   ```
   $ aws iam list-attached-role-policies --role-name <role-name-from-PhysicalResourceId-on-previous-command>
   ```

1. If there are any policies listed at all in the previous command's output, then run this final preparatory command to detach them from the role:

   ```
   $ aws iam detach-role-policy --role-name <role-name-from-PhysicalResourceId-on-previous-command> /
          --policy-arn <ARN-of-policy-discovered-in-previous-command>
   ```

1. Now you can delete the stack, which deletes all of its resources\. Pass the name of the stack that you retrieved in step 2 as the `--stack-name`:

   ```
   $ aws cloudformation delete-stack --stack-name aws-serverless-repository-MyLambdaCreationStack
   ```

   Both the IAM role and the Lambda rotation function are deleted shortly after this command runs\.

------