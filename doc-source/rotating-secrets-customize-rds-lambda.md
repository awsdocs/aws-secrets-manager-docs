# Customizing the Lambda Rotation Function Provided by Secrets Manager<a name="rotating-secrets-customize-rds-lambda"></a>

You can customize the Lambda rotation function to meet your organization's unique requirements\. Such requirements might include:
+ Add additional tests on the new version of the secret\. You want to ensure that the permissions associated with the new credentials are correct\.
+ You want to use a different strategy for rotating your secrets than the one used by the Lambda function provided by Secrets Manager\. 

To customize the function, you must first discover which function Secrets Manager created for you\. You can't see the ARN of the function in the console, but you can retrieve it by using the AWS CLI or equivalent AWS SDK operations\. 

```
$ aws secretsmanager describe-secret --secret-id MyDatabaseSecret
```

Look for the `RotationLambdaARN` value in the response\.

**To edit your Lambda rotation function**  
Follow the steps under one of the following tabs:

------
#### [ Using the console ]

1. Determine the name of the Lambda rotation function for your secret:

   1. On the list of secrets, choose the name of the secret whose rotation you want to modify\.

   1. In the **Rotation configuration** section, examine the rotation ARN\. The part of the ARN that follows `:function:` is the name\.

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Choose the name of the Lambda rotation function that you want to modify\.

1. Make the required changes to the function\. 

------
#### [ Using the AWS CLI or AWS SDK operations ]

1. Determine the name of the Lambda rotation function for your secret\. To do this, run the following command and examine the part of the `RotationLambdaARN` that follows `:function:`\.

   ```
   $ describe-secret --secret-id MySecret
   {
       "ARN": "arn:aws:secretsmanager:us-west-2:123456789012:secret:MySecret-abc123",
       "Name": "MySecret",
       "RotationLambdaARN": "arn:aws:lambda:us-west-2:123456789012:function:name_of_rotation_lambda_function",
       "LastChangedDate": 1519940941.014,
       "SecretVersionsToStages": {
           "5eae5e4a-a683-469e-96e7-af9a8180fba5": [
               "AWSCURRENT"
           ]
       }
   }
   ```

1. Examine the `RotationLambdaARN` response value\. That is the ARN of your Lambda rotation function and the last portion is the name of your function\.

1. Sign in to the AWS Management Console and open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Choose the name of the Lambda function that you identified to see the function details\.

1.  In the **Function code** section, you can edit the source code of the function\. For more information about coding a Lambda function specifically for Secrets Manager, see [Overview of the Lambda Rotation Function](rotating-secrets-lambda-function-overview.md)\. For the [https://docs.aws.amazon.com/lambda/latest/dg/](https://docs.aws.amazon.com/lambda/latest/dg/)\. All of the provided Lambda functions are written for the Python 2\.7 environment\.

------

For more information about Lambda function options and coding techniques, see the [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/)\. 

For more information about coding your own Secrets Manager rotation function, see [Understanding and Customizing Your Lambda Rotation Function](rotating-secrets-lambda-function-customizing.md)\.