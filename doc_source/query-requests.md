# Calling the API by Sending HTTP Query Requests<a name="query-requests"></a>

This section contains general information about using the Query API for AWS Secrets Manager\. For details about the API operations and errors, see the [AWS Secrets Manager API Reference](https://docs.aws.amazon.com/secretsmanager/latest/apireference/)\.

**Note**  
Instead of sending direct calls to the AWS Secrets Manager Query API, you can use one of the AWS SDKs\. The AWS SDKs consist of libraries and sample code for various programming languages and platforms such as Java, Ruby, \.NET, iOS, Android, and more\. The SDKs provide a convenient way to create programmatic access to Secrets Manager and AWS\. For example, the SDKs perform tasks such as cryptographically signing requests, managing errors, and retrying requests automatically\. For information about the AWS SDKs, including how to download and install the packages, see [Tools for Amazon Web Services](http://aws.amazon.com/tools/)\.

The Query API for AWS Secrets Manager lets you call service operations\. Query API requests are HTTPS requests that must contain an `Action` parameter to indicate the operation to be performed\. AWS Secrets Manager supports GET and POST requests for all operations\. The API doesn't require you to use GET for some operations and POST for others\. However, GET requests are subject to the limitation size of a URL\. Although this limit depends on the browser , a typical limit is 2048 bytes\. Therefore, for Query API requests that require larger sizes, you must use a POST request\.

The API returns the response in an XML document\. For details about the response, see the individual API description pages in the [AWS Organizations API Reference](https://docs.aws.amazon.com/organizations/latest/APIReference/)\.

**Topics**
+ [Endpoints](#ASMEndpoints)
+ [HTTPS Required](#IAMHTTPSRequired)
+ [Signing API Requests for Secrets Manager](#SigVersion)

## Endpoints<a name="ASMEndpoints"></a>

AWS Secrets Manager has endpoints in most AWS Regions\. For the complete list, see the [endpoint list for AWS Secrets Manager](https://docs.aws.amazon.com/general/latest/gr/rande.html#asm_region) in the *AWS General Reference*\.

For more information about AWS Regions and endpoints for all services, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/index.html?rande.html), also in the *AWS General Reference*\. 

## HTTPS Required<a name="IAMHTTPSRequired"></a>

Because the Query API returns sensitive information such as security credentials, you must use HTTPS to encrypt all API requests\. 

See the [ *AWS General Reference*\.](https://docs.aws.amazon.com/general/latest/gr/sigv4_signing.html)

## Signing API Requests for Secrets Manager<a name="SigVersion"></a>

You must sign API requests by using an access key ID and a secret access key\. We strongly recommend you don't use your AWS account root user credentials for everyday work with Secrets Manager\. Instead, you can use the credentials for an IAM user, or temporary credentials like those you use with an IAM role\.

To sign your API requests, you must use AWS Signature Version 4\. For information about using Signature Version 4, see [Signature Version 4 Signing Process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) in the *AWS General Reference*\. 

For more information, see the following:
+  [AWS Security Credentials](https://docs.aws.amazon.com/general/latest/gr/aws-security-credentials.html)\. Provides general information about the types of credentials you can use to access AWS\. 
+ [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)\. Offers suggestions for using the IAM service to help secure your AWS resources, including those in Secrets Manager\. 
+ [Temporary Credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html)\. Describes how to create and use temporary security credentials\. 