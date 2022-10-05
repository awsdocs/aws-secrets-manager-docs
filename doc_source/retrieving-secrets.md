# Retrieve secrets from AWS Secrets Manager<a name="retrieving-secrets"></a>

You can retrieve your secrets by using the console \([https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\) or the AWS CLI \([https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html)\)\.

In applications, you can retrieve your secrets by calling [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html) in any of the AWS SDKs\. However, we recommend that you cache your secret values by using client\-side caching\. Caching secrets improves speed and reduces your costs\. 
+ For Java applications: 
  + If you store database credentials in the secret, use the [Secrets Manager SQL connection drivers](retrieving-secrets_jdbc.md) to connect to a database using the credentials in the secret\. 
  + For other types of secrets, use the [Secrets Manager Java\-based caching component](retrieving-secrets_cache-java.md)\.
+ For Python applications, use the [Secrets Manager Python\-based caching component](retrieving-secrets_cache-python.md)\.
+ For \.NET applications, use the [Secrets Manager \.NET\-based caching component](retrieving-secrets_cache-net.md)\.
+ For Go applications, use the [Secrets Manager Go\-based caching component](retrieving-secrets_cache-go.md)\.
+ For JavaScript applications, call the SDK directly with [https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/SecretsManager.html#getSecretValue-property](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/SecretsManager.html#getSecretValue-property)\.
+ For PHP applications, call the SDK directly with [https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-secretsmanager-2017-10-17.html#getsecretvalue](https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-secretsmanager-2017-10-17.html#getsecretvalue)\.
+ For Ruby applications, call the SDK directly with [https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/SecretsManager/Client.html#get_secret_value-instance_method](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/SecretsManager/Client.html#get_secret_value-instance_method)\.
+ For GitHub Actions, see [Use AWS Secrets Manager secrets in GitHub jobs](retrieving-secrets_github.md)\.

## Within other systems and AWS services<a name="retrieving-secrets_services"></a>

You can also retrieve secrets within the following:
+ For AWS Batch, you can [reference secrets](integrating_BATCH.md) in a job definition\.
+  For AWS CloudFormation, you can [create secrets](cloudformation.md) and [reference secrets](cfn-example_reference-secret.md) in a CloudFormation stack\.
+ For Amazon ECS, you can [reference secrets](integrating-fargate.md) in a container definition\.
+ For Amazon EKS, you can use [AWS Secrets and Configuration Provider \(ASCP\)](integrating_csi_driver.md) to mount secrets as files in Amazon EKS\.
+ For GitHub, you can use the [Secrets Manager GitHub action](retrieving-secrets_github.md) to add secrets as environment variables in your GitHub jobs\.
+ For AWS IoT Greengrass, you can [reference secrets](integrating-greengrass.md) in a Greengrass group\.
+ For AWS Lambda, you can [reference secrets](integrating-lambda.md) in a Lambda function\.
+ For Parameter Store, you can [reference secrets](integrating_parameterstore.md) in a parameter\.