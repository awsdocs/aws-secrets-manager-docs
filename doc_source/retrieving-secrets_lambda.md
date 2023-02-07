# Use AWS Secrets Manager secrets in AWS Lambda functions<a name="retrieving-secrets_lambda"></a>

You can use the AWS Parameters and Secrets Lambda Extension to retrieve and cache AWS Secrets Manager secrets in Lambda functions without using an SDK\. Retrieving a cached secret is faster than retrieving it from Secrets Manager\. Because there is a cost for calling Secrets Manager APIs, using a cache can reduce your costs\. The extension can retrieve both Secrets Manager secrets and Parameter Store parameters\. For information about Parameter Store, see [Parameter Store integration with Lambda extensions](https://docs.aws.amazon.com/systems-manager/latest/userguide/ps-integration-lambda-extensions.html) in the *AWS Systems Manager User Guide*\. 

A Lambda extension is a companion process that adds to the capabilities of a Lambda function\. For more information, see [Lambda extensions](https://docs.aws.amazon.com/lambda/latest/dg/runtimes-extensions-api.html) in the *Lambda Developer Guide*\. Lambda logs execution information about the extension along with the function by using Amazon CloudWatch Logs\. By default, the extension logs a minimal amount of information to CloudWatch\. To log more details, set the [environment variable](#retrieving-secrets_lambda_env-var) `PARAMETERS_SECRETS_EXTENSION_LOG_LEVEL` to `debug`\.

The extension makes requests to localhost port 2773\. You can configure the port by setting the [environment variable](#retrieving-secrets_lambda_env-var) `PARAMETERS_SECRETS_EXTENSION_HTTP_PORT`\. 

Lambda instantiates separate instances corresponding to the concurrency level that your function requires\. Each instance is isolated and maintains its own local cache of your configuration data\. For more information about Lambda instances and concurrency, see [Managing concurrency for a Lambda function](https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html) in the *Lambda Developer Guide*\.

To add the extension for ARM, you must use the `arm64` architecture for your Lambda function\. For more information, see [Lambda instruction set architectures](https://docs.aws.amazon.com/lambda/latest/dg/foundation-arch.html) in the *Lambda Developer Guide*\. The extension supports ARM in the following Regions: Asia Pacific \(Mumbai\), US East \(Ohio\), Europe \(Ireland\), Europe \(Frankfurt\), Europe \(Zurich\), US East \(N\. Virginia\), Europe \(London\), Europe \(Spain\), Asia Pacific \(Tokyo\), US West \(Oregon\), Asia Pacific \(Singapore\), Asia Pacific \(Hyderabad\), and Asia Pacific \(Sydney\)\.

**To use the AWS Parameters and Secrets Lambda Extension**

1. Add the layer to your function by doing one of the following:
   + Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

     1. Choose your function, choose **Layers**, and then choose **Add a layer**\.

     1. On the **Add layer** page, for **AWS layers**, choose **AWS Parameters and Secrets Lambda Extension**, and then choose **Add**\.
   + Use the following AWS CLI command with the appropriate [ARN for your Region](#retrieving-secrets_lambda_ARNs)\.

     ```
     aws lambda update-function-configuration \
         --function-name my-function \
         --layers LayerARN
     ```

1. Grant permissions to the Lambda [execution role](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html) to be able to access secrets:
   +  `secretsmanager:GetSecretValue` permission for the secret\. See [Example: Permission to retrieve secret values](auth-and-access_examples.md#auth-and-access_examples_read)\.
   + \(Optional\) If the secret is encrypted with a customer managed key instead of the AWS managed key `aws/secretsmanager`, the execution role also needs `kms:Decrypt` permission for the KMS key\.
   + You can use Attribute Based Access Control \(ABAC\) with the Lambda role to allow for more granular access to secrets in the account\. For more information, see [Example: Control access to secrets using tags](auth-and-access_examples.md#tag-secrets-abac) and [Example: Limit access to identities with tags that match secrets' tags](auth-and-access_examples.md#auth-and-access_tags2)\.

1. Configure the cache with Lambda [environment variables](#retrieving-secrets_lambda_env-var)\. 

1. To retrieve secrets from the extension cache, you first need to add the `X-AWS-Parameters-Secrets-Token` to the request header\. Set the token to `AWS_SESSION_TOKEN`, which is provided by Lambda for all running functions\. Using this header indicates that the caller is within the Lambda environment\.

   The following Python example shows how to add the header\. 

   ```
   import os
   headers = {"X-Aws-Parameters-Secrets-Token": os.environ.get('AWS_SESSION_TOKEN')}
   ```

1. To retrieve a secret within the Lambda function, use one of the following HTTP GET requests:
   + To retrieve a secret, for `secretId`, use the ARN or name of the secret\.

     ```
     GET: /secretsmanager/get?secretId=secretId
     ```
   + To retrieve the previous secret value or a specific version by staging label, for `secretId`, use the ARN or name of the secret, and for `versionStage`, use the staging label\.

     ```
     GET: /secretsmanager/get?secretId=secretId&versionStage=AWSPREVIOUS
     ```
   + To retrieve a specific secret version by ID, for `secretId`, use the ARN or name of the secret, and for `versionId`, use the version ID\.

     ```
     GET: /secretsmanager/get?secretId=secretId&versionId=versionId
     ```  
**Example Retrieve a secret \(Python\)**  

   The following Python example shows how to retrieve a secret and parse the result using [https://docs.python.org/3/library/json.html](https://docs.python.org/3/library/json.html)\.

   ```
   secrets_extension_endpoint = "http://localhost:" + \
       secrets_extension_http_port + \
       "/secretsmanager/get?secretId=" + \
       <secret_name>
     
     r = requests.get(secrets_extension_endpoint, headers=headers)
     
     secret = json.loads(r.text)["SecretString"] # load the Secrets Manager response into a Python dictionary, access the secret
   ```

## AWS Parameters and Secrets Lambda Extension environment variables<a name="retrieving-secrets_lambda_env-var"></a>

You can configure the extension with the following environment variables\.

For information about how to use environment variables, see [Using Lambda environment variables](https://docs.aws.amazon.com/lambda/latest/dg/configuration-envvars.html) in the *Lambda Developer Guide*\. 

`PARAMETERS_SECRETS_EXTENSION_CACHE_ENABLED`  
Set to true to cache parameters and secrets\. Set to false for no caching\. Default is true\.

`PARAMETERS_SECRETS_EXTENSION_CACHE_SIZE`  
The maximum number of secrets and parameters to cache\. Must be a value from 0 to 1000\. A value of 0 means there is no caching\. This variable is ignored if both `SSM_PARAMETER_STORE _TTL` and `SECRETS_MANAGER_TTL` are 0\. Default is 1000\.

`PARAMETERS_SECRETS_EXTENSION_HTTP_PORT`  
The port for the local HTTP server\. Default is 2773\.

`PARAMETERS_SECRETS_EXTENSION_LOG_LEVEL`  
The level of logging the extension provides: `debug`, `info`, `warn`, `error`, or `none`\. Set to `debug` to see the cache configuration\. Default is `info`\.

`PARAMETERS_SECRETS_EXTENSION_MAX_CONNECTIONS`  
Maximum number of connections for HTTP clients that the extension uses to make requests to Parameter Store or Secrets Manager\. This is a per\-client configuration\. Default is 3\.

`SECRETS_MANAGER_TIMEOUT_MILLIS`  
Timeout for requests to Secrets Manager in milliseconds\. A value of 0 means there is no timeout\. Default is 0\.

`SECRETS_MANAGER_TTL`  
TTL of a secret in the cache in seconds\. A value of 0 means there is no caching\. The maximum is 300 seconds\. This variable is ignored if `PARAMETERS_SECRETS_CACHE_SIZE` is 0\. Default is 300 seconds\.

`SSM_PARAMETER_STORE_TIMEOUT_MILLIS`  
Timeout for requests to Parameter Store in milliseconds\. A value of 0 means there is no timeout\. Default is 0\.

`SSM_PARAMETER_STORE_TTL`  
TTL of a parameter in the cache in seconds\. A value of 0 means there is no caching\. The maximum is 300 seconds\. This variable is ignored if `PARAMETERS_SECRETS_CACHE_SIZE` is 0\. Default is 300 seconds\.

## AWS Parameters and Secrets Lambda Extension ARNs<a name="retrieving-secrets_lambda_ARNs"></a>


****  

| Region | ARN | 
| --- | --- | 
|  US East \(N\. Virginia\)  |  `arn:aws:lambda:us-east-1:177933569100:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:us-east-1:177933569100:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:4`  | 
|  US East \(Ohio\)  |  `arn:aws:lambda:us-east-2:590474943231:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:us-east-2:590474943231:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:4`  | 
|  US West \(N\. California\)  |  `arn:aws:lambda:us-west-1:997803712105:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:us-west-1:997803712105:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:1`  | 
|  US West \(Oregon\)  |  `arn:aws:lambda:us-west-2:345057560386:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:us-west-2:345057560386:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:4`  | 
|  Canada \(Central\)  |  `arn:aws:lambda:ca-central-1:200266452380:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:ca-central-1:200266452380:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:1`  | 
|  Europe \(Frankfurt\)  |  `arn:aws:lambda:eu-central-1:187925254637:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:eu-central-1:187925254637:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:4`  | 
|  Europe \(Zurich\)  |  `arn:aws:lambda:eu-central-2:772501565639:layer:AWS-Parameters-and-Secrets-Lambda-Extension:1`  | 
|  Europe \(Ireland\)  |  `arn:aws:lambda:eu-west-1:015030872274:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:eu-west-1:015030872274:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:4`  | 
|  Europe \(London\)  |  `arn:aws:lambda:eu-west-2:133256977650:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:eu-west-2:133256977650:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:4`  | 
|  Europe \(Paris\)  |  `arn:aws:lambda:eu-west-3:780235371811:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:eu-west-3:780235371811:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:1`  | 
|  Europe \(Stockholm\)  |  `arn:aws:lambda:eu-north-1:427196147048:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:eu-north-1:427196147048:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:1`  | 
|  Europe \(Milan\)  |  `arn:aws:lambda:eu-south-1:325218067255:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:eu-south-1:325218067255:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:1`  | 
|  Europe \(Spain\)  |  `arn:aws:lambda:eu-south-2:524103009944:layer:AWS-Parameters-and-Secrets-Lambda-Extension:1`  | 
| China \(Beijing\) |  `arn:aws-cn:lambda:cn-north-1:287114880934:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4`  | 
| China \(Ningxia\) |  `arn:aws-cn:lambda:cn-northwest-1:287310001119:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4`  | 
|  Asia Pacific \(Hong Kong\)  |  `arn:aws:lambda:ap-east-1:768336418462:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:ap-east-1:768336418462:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:1`  | 
|  Asia Pacific \(Hyderabad\)  |  `arn:aws:lambda:ap-south-2:070087711984:layer:AWS-Parameters-and-Secrets-Lambda-Extension:1`  | 
|  Asia Pacific \(Tokyo\)  |  `arn:aws:lambda:ap-northeast-1:133490724326:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:ap-northeast-1:133490724326:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:4`  | 
| Asia Pacific \(Osaka\) |  `arn:aws:lambda:ap-northeast-3:576959938190:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4`  | 
|  Asia Pacific \(Seoul\)  |  `arn:aws:lambda:ap-northeast-2:738900069198:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:ap-northeast-2:738900069198:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:1`  | 
|  Asia Pacific \(Singapore\)  |  `arn:aws:lambda:ap-southeast-1:044395824272:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:ap-southeast-1:044395824272:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:4`  | 
|  Asia Pacific \(Sydney\)  |  `arn:aws:lambda:ap-southeast-2:665172237481:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:ap-southeast-2:665172237481:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:4`  | 
|  Asia Pacific \(Jakarta\)  |  `arn:aws:lambda:ap-southeast-3:490737872127:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:ap-southeast-3:490737872127:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:1`  | 
|  Asia Pacific \(Mumbai\)  |  `arn:aws:lambda:ap-south-1:176022468876:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:ap-south-1:176022468876:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:4`  | 
|  South America \(São Paulo\)  |  `arn:aws:lambda:sa-east-1:933737806257:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:sa-east-1:933737806257:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:1`  | 
|  Africa \(Cape Town\)  |  `arn:aws:lambda:af-south-1:317013901791:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:af-south-1:317013901791:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:1`  | 
| Middle East \(UAE\) | arn:aws:lambda:me\-central\-1:858974508948:layer:AWS\-Parameters\-and\-Secrets\-Lambda\-Extension:4 | 
|  Middle East \(Bahrain\)  |  `arn:aws:lambda:me-south-1:832021897121:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4` `arn:aws:lambda:me-south-1:832021897121:layer:AWS-Parameters-and-Secrets-Lambda-Extension-Arm64:1`  | 
| AWS GovCloud \(US\-East\) |  `arn:aws-us-gov:lambda:us-gov-east-1:129776340158:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4`  | 
| AWS GovCloud \(US\-West\) |  `arn:aws-us-gov:lambda:us-gov-west-1:127562683043:layer:AWS-Parameters-and-Secrets-Lambda-Extension:4`  | 