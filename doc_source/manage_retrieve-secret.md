# Retrieving the Secret Value<a name="manage_retrieve-secret"></a>

One of Secrets Manager's primary purposes is to enable you to programmatically and securely retrieve your secrets in your custom applications\. However, you can also retrieve them using the console or the CLI tools\.

This section includes procedures and commands that describe how to retrieve the secret value of a secret\.<a name="proc-secret-value"></a>

**To retrieve a secret value**  
Follow the steps under one of the following tabs:

------
#### [ Using the console ]<a name="proc-secret-value-console"></a>

**Minimum permissions**  
To retrieve a secret in the console you must have these permissions:  
`secretsmanager:ListSecrets` – to navigate to the secret you want to retrieve
`secretsmanager:GetSecretValue` — to retrieve the non\-encrypted parts of the secret
`secretsmanager:GetSecretValue` – to retrieve the encrypted part of the secret
`kms:Decrypt` – Only required if you used a custom KMS CMK to encrypt your secret

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the list of secrets in your account, choose the name of the secret that you want to view\.

   The **Secret details** page appears, displaying all of the chosen secret's configuration details except for the encrypted secret text\.

1. In the **Credential data** section, choose **Retrieve credentials**\.

1. Choose **secret key : secret value** to see the credentials parsed out as individual keys and values\. Choose **Plaintext** to see the original JSON text string that is encrypted and stored\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-secret-value-api"></a>

For more information about retrieving a secret from your application's code, see [Retrieving the Secret Value](#manage_retrieve-secret)\.

You can use the following commands to retrieve a secret stored in AWS Secrets Manager:
+ **API/SDK:** [http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html)
+ **CLI:** [http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html](http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html)

You must identify the secret by its friendly name or ARN and specify the version of the secret to return \(it defaults to the version labeled `AWSCURRENT` if you don't otherwise specify a version\)\. The contents of the secret text are returned in the response parameters `PlaintextString` and, if you stored any binary data in the secret, `Plaintext`, which returns a byte array\.

**Example**  
The following example of the CLI command decrypts and retrieves the encrypted secret information from the default version of the secret named "MyTestDatabase"\.  

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
```

------