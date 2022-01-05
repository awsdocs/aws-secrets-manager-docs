# Access Secrets Manager<a name="asm_access"></a>

**Topics**
+ [Secrets Manager console](#asm-console)
+ [Command line tools](#asm-cli)
+ [AWS SDKs](#asm-sdks)
+ [HTTPS Query API](#asm-sdks_query-api)

## Secrets Manager console<a name="asm-console"></a>

You can manage your secrets using the browser\-based [Secrets Manager console](https://console.aws.amazon.com/secretsmanager/) and perform almost any task related to your secrets by using the console\.

## Command line tools<a name="asm-cli"></a>

The AWS command line tools allows you to issue commands at your system command line to perform Secrets Manager and other AWS tasks\. This can be faster and more convenient than using the console\. The command line tools can be useful if you want to build scripts to perform AWS tasks\.

AWS provides two sets of command line tools: 
+ [AWS Command Line Interface \(AWS CLI\)](https://docs.aws.amazon.com/cli/latest/userguide/) 
+ [AWS Tools for Windows PowerShell](https://docs.aws.amazon.com/powershell/latest/userguide/)

## AWS SDKs<a name="asm-sdks"></a>

The AWS SDKs consist of libraries and sample code for various programming languages and platforms, for example, Java, Python, Ruby, \.NET, and others\. The SDKs include tasks such as cryptographically signing requests, managing errors, and retrying requests automatically\. For more information, see [AWS SDKs](#asm-sdks)\.

To download and install any of the SDKs, see [Tools for Amazon Web Services](https://aws.amazon.com/tools/#sdk)\.

For SDK documentation, see:
+ [C\+\+](http://sdk.amazonaws.com/cpp/api/LATEST/namespace_aws_1_1_secrets_manager.html)
+ [Java](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/package-summary.html)
+ [PHP](https://docs.aws.amazon.com//aws-sdk-php/v3/api/namespace-Aws.SecretsManager.html)
+ [Python](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html)
+ [Ruby](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/SecretsManager.html)
+ [Node\.js](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/SecretsManager.html)

## HTTPS Query API<a name="asm-sdks_query-api"></a>

The HTTPS Query API gives you [programmatic access](https://docs.aws.amazon.com/secretsmanager/latest/apireference/Welcome.html) to Secrets Manager and AWS\. The HTTPS Query API allows you to issue HTTPS requests directly to the service\. 

Although you can make direct calls to the Secrets Manager HTTPS Query API, we recommend that you use one of the SDKs instead\. The SDK performs many useful tasks you otherwise must perform manually\. For example, the SDKs automatically sign your requests and convert responses into a structure syntactically appropriate to your language\.