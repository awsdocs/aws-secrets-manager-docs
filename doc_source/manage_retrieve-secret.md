# Retrieving the Secret Value<a name="manage_retrieve-secret"></a>

One of the main purposes of Secrets Manager is to enable you to programmatically and securely retrieve your secrets in your custom applications\. However, you can also retrieve them by using the console or the CLI tools\.

This section includes procedures and commands that describe how to retrieve the secret value of a secret\.<a name="proc-secret-value"></a>

**To retrieve a secret value**  
Follow the steps under one of the following tabs:

------
#### [ Using the console ]<a name="proc-secret-value-console"></a>

**Minimum permissions**  
To retrieve a secret in the console, you must have these permissions:  
`secretsmanager:ListSecrets` – Used to navigate to the secret you want to retrieve\.
`secretsmanager:DescribeSecret` — Used to retrieve the non\-encrypted parts of the secret\.
`secretsmanager:GetSecretValue` – Used to retrieve the encrypted part of the secret\.
`kms:Decrypt` – Required only if you used a custom AWS KMS customer master key \(CMK\) to encrypt your secret\.

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the list of secrets in your account, choose the name of the secret that you want to view\.

   The **Secret details** page appears\. It displays all of the chosen secret's configuration details except for the encrypted secret text\.

1. In the **Secret value** section, choose **Retrieve secret value**\.

1. Choose **Secret key/value** to see the credentials parsed out as individual keys and values\. Choose **Plaintext** to see the original JSON text string that's encrypted and stored\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-secret-value-api"></a>

For more information about retrieving a secret from your application's code, see [Retrieving the Secret Value](#manage_retrieve-secret)\.

You can use the following commands to retrieve a secret stored in AWS Secrets Manager:
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html)
+ **AWS CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html)

You must identify the secret by its friendly name or Amazon Resource Name \(ARN\), and specify the version of the secret to return\. \(It defaults to the version that has the staging label `AWSCURRENT` if you don't otherwise specify a version\)\. The contents of the secret text are returned in the response parameters `PlaintextString` and, if you stored any binary data in the secret, `Plaintext`, which returns a byte array\.

**Example**  
The following example of the AWS CLI command decrypts and retrieves the encrypted secret information from the default version of the secret named "MyTestDatabase"\.  

```
$ aws secretsmanager get-secret-value --secret-id development/MyTestDatabase
{
    "SecretARN": "arn:aws:secretsmanager:region:accountid:secret:development/MyTestDatabase-AbCdEf",
    "SecretName": "development/MyTestDatabase",
    "SecretVersionId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE",
    "SecretString": "{\"ServerName\":\"MyDBServer\",\"UserName\":\"Anaya\",\"Password\":\"MyT0pSecretP@ssw0rd\"}",
    "SecretVersionStages": [
        "AWSCURRENT"
    ],
    "CreatedDate": 1510089380.309
}
```<a name="use-client-side-caching-components"></a>

**Use the AWS\-developed open source client\-side caching components to improve performance and reduce your costs**  
To efficiently use your secret, you must not simply retrieve the secret value every time you need to use it\. You should also include code that performs the following operations:
+ Your app should cache a secret after retrieving it\. Then, instead of going across the network to retrieve the secret from Secrets Manager every time you need it, use the cached value\. You should retrieve and update the secret value from Secrets Manager only when the cached value no longer works and you get an "Access Denied" error message because Secrets Manager rotated the credentials\.
+ Whenever your code receives a network or AWS service error, retry your requests using an algorithm that implements an exponential backoff and retry with jitter, as described at [Exponential Backoff And Jitter](http://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/) in the *AWS Architecture Blog*\.

By writing your code to perform those tasks, you get the following benefits:
+ **Reduced costs **– For secrets that you use often, retrieving them from the cache reduces the number of API calls your apps make to Secrets Manager\. Because Secrets Manager charges a fee per API call, this can significantly reduce your costs\.
+ **Improved performance** – Retrieving the secret from memory instead of having to send a request over the Internet to Secrets Manager can dramatically improve the performance of your apps\.
+ **Improved availability** – Transient network errors can be much less of a problem when you retrieve your secret information from the cache\. 

Secrets Manager has created a client\-side component that implements these best practices to simplify your creation of code that accesses secrets stored in AWS Secrets Manager\. All you need to do is to install the client and then call it, providing the identifier of the secret that you want the code to use\. The only other requirement is to ensure that the AWS credentials you use to call Secrets Manager have the `secretsmanager:DescribeSecret` and `secretsmanager:GetSecretValue` permissions on that secret\. Those credentials would typically be associated with an IAM role\. That role might be assigned to you at sign\-on time if you use [SAML federation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_saml.html) or [web identity \(OIDC\) federation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_oidc.html)\. If your app is running on an Amazon EC2 instance, then your administrator can assign an IAM role by creating an [instance profile](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html)\. 

The component comes in the following forms:
+ Client\-side libraries in Java, Python, Go, and \.NET that you interact with instead of directly calling the `GetSecretValue` operation\.
+ A database driver component that is compliant with Java Database Connectivity \(JDBC\)\. This component is a wrapper around the true JDBC driver that adds the functionality described above\.

For instructions to download and use one of the following links:
+ [Java\-based caching client component](https://github.com/aws/aws-secretsmanager-caching-java )
+ [JDBC\-compatible database connector component](https://github.com/aws/aws-secretsmanager-jdbc )
+ [Python caching client](https://github.com/aws/aws-secretsmanager-caching-python )
+ [Caching client for \.NET](https://github.com/aws/aws-secretsmanager-caching-net )
+ [Go caching client](https://github.com/aws/aws-secretsmanager-caching-go )

------