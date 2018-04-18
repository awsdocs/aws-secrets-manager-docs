# Enabling Rotation for a Secret for Another Database or Service<a name="enable-rotation-other"></a>

To configure rotation of a secret for a database other than the supported RDS databases or some other service you must manually perform a few extra steps\. Primarily, you must create and provide the code for the Lambda rotation function\. 

**Warning**  
Configuring rotation causes the secret to rotate once as soon as you store the secret\. Before you do this, you must ensure that all of your applications that use the credentials stored in the secret are updated to retrieve the secret from AWS Secrets Manager\. The old credentials might no longer be usable after the initial rotation and any applications that you fail to update will break as soon as the old credentials are no longer valid\.

You must have already created your Lambda rotation function\. If you have not yet created the function then perform the steps found at [Rotating AWS Secrets Manager Secrets for Other Databases or Services](rotating-secrets-create-generic-template.md)\. Return to this procedure when the function is created and ready to associate with your secret\.

**To enable and configure rotation for a secret for another database or service**  
Follow the steps under one of the following tabs:

------
#### [ Using the console ]<a name="proc-enable-rotation-other-console"></a>

**Minimum permissions**  
To enable and configure rotation in the console you must have these permissions:  
`secretsmanager:ListSecrets` \- to see the list of secrets in the console
`secretsmanager:DescribeSecrets` \- to access the details page for your chosen secret
`secretsmanager:RotateSecret` \- to configure or trigger rotation

1. Sign in to the AWS Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose the name of the secret for which you want to enable rotation\.

1. In the **Configure automatic rotation** section, choose **Enable automatic rotation**\. This enables the other controls in this section\.

1. For **Select rotation interval**, choose one of the predefined values or choose **Custom** and then type the number of days you want between rotations\.

1. For **Choose an AWS Lambda function**, choose your rotation function from the drop\-down list\. If you have not yet created the function then perform the steps found at [Rotating AWS Secrets Manager Secrets for Other Databases or Services](rotating-secrets-create-generic-template.md)\. Return and perform this step when the function is created and ready to associate with your secret\.

------
#### [ Using the AWS CLI or AWS SDKs ]<a name="proc-enable-rotation-other-api"></a>

**Minimum permissions**  
To create a Lambda function using the console you must have these permissions:  
`lambda:CreateFunction` \- to create the function in AWS Lambda
`lambda:InvokeFunction` \- to attach the rotation function to the secret
`secretsmanager:DescribeSecrets` \- to access the secret details page
`secretsmanager:RotateSecret` \- to attach the rotation function to the secret or to trigger rotation

You can use the following commands to enable and configure rotation in AWS Secrets Manager:
+ **API/SDK:** [http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RotateSecret](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RotateSecret)
+ **CLI:** [http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/rotate-secret.html](http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/rotate-secret.html)

**Example**  
Here is an example CLI command that performs the equivalent of the console\-based secret creation in the previous section\. It sets the rotation interval to 30 days, and specifies the ARN of a second secret that has permissions to change this secret's credentials on the database\.  

```
$ aws secretsmanager rotate-secret --secret-id production/MyAwesomeAppSecret --automatically-rotate-after-days 30 --rotation-lambda-arn arn:aws:secretsmanager:region:accountid:secret:production/MasterSecret-AbCdEf
{
    "ARN": "arn:aws:secretsmanager:region:accountid:secret:production/MyAwesomeAppSecret-AbCdEf",
    "Name": "production/MyAwesomeAppSecret",
    "VersionId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE"
}
```

The `ClientRequestToken` parameter is not required because we are using the AWS CLI which automatically generates and supplies one for us\. The output includes the secret version ID of the new version that is created during the initial rotation\. After rotation completes, this new version has the staging label `AWSCURRENT` attached and the previous version has the staging label `AWSPREVIOUS`\.

------