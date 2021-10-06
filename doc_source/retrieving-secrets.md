# Retrieve secrets<a name="retrieving-secrets"></a>

With Secrets Manager, you can programmatically and securely retrieve your secrets in your applications\. You can also retrieve your secrets by using the console or the AWS CLI\.<a name="proc-secret-value"></a>

To retrieve a secret in the console, you must have these permissions:
+ `secretsmanager:ListSecrets` – Use to navigate to the secret to retrieve\.
+ `secretsmanager:DescribeSecret` — Use to retrieve the non\-encrypted parts of the secret\.
+ `secretsmanager:GetSecretValue` – Use to retrieve the encrypted part of the secret\.
+ `kms:Decrypt` – Required only if you used a customer managed key instead of the AWS managed key \(`aws/secretsmanager`\) to encrypt your secret\.

**To retrieve a secret \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the **Secrets** page, choose your secret\.

1. On the **Secret details** page, in the **Secret value** section, choose **Retrieve secret value**\.

1. Do one of the following:
   + Choose **Secret key/value** to see the credentials as individual keys and values\. 
   + Choose **Plaintext** to see the JSON text string that Secrets Manager encrypts and stores\.

## Retrieve secrets programmatically<a name="retrieving-secrets_pro"></a>

You can use the following commands to retrieve a secret stored in AWS Secrets Manager:
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html)
  + [C\+\+](http://sdk.amazonaws.com/cpp/api/LATEST/namespace_aws_1_1_secrets_manager.html)
  + [Java](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/package-summary.html)
  + [PHP](https://docs.aws.amazon.com//aws-sdk-php/v3/api/namespace-Aws.SecretsManager.html)
  + [Python](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html)
  + [Ruby](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/SecretsManager.html)
  + [Node\.js](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/SecretsManager.html)
+ **AWS CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html)

You identify the secret by the name or ARN\. You can include the version, but if you don't specify a version, Secrets Manager defaults to the version with the staging label `AWSCURRENT`\. Secrets Manager returns the contents of the secret text in the response parameters `PlaintextString`\. If you stored binary data in the secret, Secrets Manager also returns `Plaintext`, a byte array\. Secrets Manager uses the last modified date for the `CreatedDate` output\.