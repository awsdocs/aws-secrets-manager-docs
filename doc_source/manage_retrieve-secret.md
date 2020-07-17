# Retrieving the Secret Value<a name="manage_retrieve-secret"></a>

Secrets Manager enables you to programmatically and securely retrieve your secrets in your custom applications\. However, you can also retrieve your secrets by using the console or the CLI tools\.

This section includes procedures and commands describing how to retrieve the secret value of a secret\.<a name="proc-secret-value"></a>

**Retrieving a secret value**  
Follow the steps on one of the following tabs:

------
#### [ Using the Secrets Manager console ]<a name="proc-secret-value-console"></a>

**Minimum permissions**  
To retrieve a secret in the console, you must have these permissions:  
`secretsmanager:ListSecrets` – Use to navigate to the secret to retrieve\.
`secretsmanager:DescribeSecret` — Use to retrieve the non\-encrypted parts of the secret\.
`secretsmanager:GetSecretValue` – Use to retrieve the encrypted part of the secret\.
`kms:Decrypt` – Required only if you used a custom AWS KMS customer master key \(CMK\) to encrypt your secret\.

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. From the list of secrets in your account, choose the name of the secret to view\.

   The **Secret details** page appears\. The page displays all of the chosen secret configuration details except for the encrypted secret text\.

1. In the **Secret value** section, choose **Retrieve secret value**\.

1. Choose **Secret key/value** to see the credentials parsed out as individual keys and values\. Choose **Plaintext** to see the JSON text string encrypted and stored\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-secret-value-api"></a>

You can use the following commands to retrieve a secret stored in AWS Secrets Manager:
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html)
+ **AWS CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html)

You must identify the secret by the friendly name or Amazon Resource Name \(ARN\), and specify the version of the secret\. If you don't specify a version, Secrets Manager defaults to the version with the staging label `AWSCURRENT`\. Secrets Manager returns the contents of the secret text in the response parameters `PlaintextString` and, if you stored any binary data in the secret, `Plaintext`, which returns a byte array\.

**Example**  
The following example of the AWS CLI command decrypts and retrieves the encrypted secret information from the default version of the secret named "MyTestDatabase"\. Secrets Manager uses *the last modified date* for the `CreatedDate` output\.  

```
$ aws secretsmanager get-secret-value --secret-id development/MyTestDatabase
{
    "ARN": "arn:aws:secretsmanager:region:accountid:secret:development/MyTestDatabase-AbCdEf",
    "Name": "development/MyTestDatabase",
    "VersionId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE",
    "SecretString": "{\"ServerName\":\"MyDBServer\",\"UserName\":\"Anaya\",\"Password\":\"MyT0pSecretP@ssw0rd\"}",
    "SecretVersionStages": [
        "AWSCURRENT"
    ],
    "CreatedDate": 1510089380.309
}
```

------