# Modifying a Secret<a name="manage_update-secret"></a>

You can modify some elements of a secret after you create it\. In the console, you can edit the description, edit or attach a new resource\-based policy to modify permissions to the secret, change the AWS KMS customer master key \(CMK\) that's used to encrypt and decrypt the protected secret information, and edit or add or remove tags\.

You can also change the value of the encrypted secret information\. However, we recommend that you use rotation to update secret values that contain credentials\. Rotation doesn't just update the secret\. It also modifies the credentials on the protected database or service to match those in the secret\. This keeps them automatically synchronized so that when clients request a secret value, they always retrieve a working set of credentials\.

This section includes procedures and commands that describe how to modify the following elements of a secret:
+ [Encrypted secret value](#proc-encrypted-secret-value)
+ [Description](#proc-description)
+ [AWS KMS encryption key](#proc-kmskey)
+ [Tags](#proc-tags)
+ [Permission policy](manage_secret-policy.md)<a name="proc-encrypted-secret-value"></a>

**To modify the encrypted secret value stored in a secret**  
Follow the steps under one of the following tabs:

**Important**  
Updating the secret in this manner doesn't change the credentials on the protected server\. If you want the credentials on the server to stay in sync with the credentials stored in the secret value, we recommend that you enable rotation\. An AWS Lambda function changes both the credentials on the server and those in the secret to match, and tests that the updated credentials work\. For more information, see [Rotating Your AWS Secrets Manager Secrets](rotating-secrets.md)\.
You can create a basic secret using whatever format for `SecretString` that you want\. For example, you could use a simple JSON key\-value pair\. For example, `{"username":"someuser", "password":"securepassword"}` However, if you later want to enable rotation for this secret, you must use a specific structure for the secret\. The exact format is determined by the rotation function you want to use with this secret\. For the details of what each rotation function requires to work with the secret value, see the **Expected SecretString Value** entry under the relevant rotation function at [AWS Templates You Can Use to Create Lambda Rotation Functions ](reference_available-rotation-templates.md)\.

------
#### [ Using the console ]<a name="proc-encrypted-secret-value-console"></a>

When you update the encrypted secret value in a secret, you create a new version of the secret\. The new version automatically gets the staging label `AWSCURRENT` moved to it\. The old version is still accessible by querying for the staging label `AWSPREVIOUS`\. This isn't the same as rotation\.

**Note**  
Any time the staging label `AWSCURRENT` moves from one version to another, Secrets Manager automatically moves the staging label `AWSPREVIOUS` to the version that `AWSCURRENT` was just removed from\.

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the list of secrets, choose the name of the secret with the secret value that you want to modify\.

1. In the **Secret value** section, choose **Retrieve secret value**\.

1. With the secret value now displayed, choose `Edit`\.

1. Update the values as appropriate, and then choose **Save**\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-encrypted-secret-value-api"></a>

You can use the following commands to update the encrypted secret value that's stored in the secret\. When you update the encrypted secret value in a secret, you create a new version of the secret\.

**Important**  
You can update a basic secret using whatever format for `SecretString` that you want\. For example, you could use a simple JSON key\-value pair\. For example, `{"username":"someuser", "password":"securepassword"}` However, if you later want to enable rotation for this secret then you must use the specific structure expected by the rotation function that you use with this secret\. For the details of what each rotation function requires to work with the secret value, see the **Expected SecretString Value** entry under the relevant rotation function at [ AWS Templates You Can Use to Create Lambda Rotation Functions Rotation Function Templates  AWS Secrets Manager provides several AWS managed templates that you can use to create a Lambda rotation function\.   This section identifies the AWS managed templates that you can use to create a Lambda rotation function for your AWS Secrets Manager secret\. These templates are associated with the AWS Serverless Application Repository, which uses AWS CloudFormation to create 'stacks' of preconfigured resources\. In this case, they create a stack that consists of the Lambda function and an IAM role that Secrets Manager can assume to invoke the function when rotation occurs\. **To create a Lambda rotation function with any of the following templates, you can copy and paste the ARN of the specified template into the CLI commands described in the topic [Rotating AWS Secrets Manager Secrets for Other Databases or Services](rotating-secrets-create-generic-template.md)\.** Each of the following templates creates a Lambda rotation function for a different combination of database and rotation strategy\. The first bullet under each shows the database or service that the function supports\. The second bullet describes the rotation strategy that's implemented by the function\. The third bullet specifies the JSON structure that the rotation function expects to find in the `SecretString` value of the secret being rotated\. **RDS databases**   [RDS MariaDB Single User](#sar-template-mariadb-singleuser)   [RDS MariaDB Master User](#sar-template-mariadb-multiuser)   [RDS MySQL Single User](#sar-template-mysql-singleuser)   [RDS MySQL Master User](#sar-template-mysql-multiuser)   [RDS Oracle Single User](#sar-template-oracle-singleuser)   [RDS Oracle Master User](#sar-template-oracle-multiuser)   [RDS PostgreSQL Single User](#sar-template-postgre-singleuser)   [RDS PostgreSQL Master User](#sar-template-postgre-multiuser)   [RDS Microsoft SQLServer Single User](#sar-template-sqlserver-singleuser)   [RDS Microsoft SQLServer Master User](#sar-template-sqlserver-multiuser)   **Other databases and services**   [Generic Rotation Function Template](#sar-template-generic)    Templates for Databases Running on Amazon RDS   RDS MariaDB Single User  

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSMariaDBRotationSingleUser
```   **Name:** SecretsManagerRDSMariaDBRotationSingleUser   **Supported database/service:** MariaDB database that's hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.   **Rotation strategy:** This changes the password for a user whose credentials are stored in the secret that's rotated\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)\.   **Expected `SecretString` structure:**  

  ```
  {
    "engine": "mariadb",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to None>",
    "port": "<optional: TCP port number. If not specified, defaults to 3306>"
  }
  ```   [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMariaDBRotationSingleUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMariaDBRotationSingleUser/lambda_function.py)     RDS MariaDB Master User  

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSMariaDBRotationMultiUser
```   **Name:** SecretsManagerRDSMariaDBRotationMultiUser   **Supported database/service:** MariaDB database that's hosted on an Amazon RDS database instance\.   **Rotation strategy:** Two users are alternated during rotation by using the credentials of a separate master user, which is stored in a separate secret\. The user that's not currently active has its password changed before it's made the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.   **Expected `SecretString` structure:**  

  ```
  {
    "engine": "mariadb",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to None>",
    "port": "<optional: TCP port number. If not specified, defaults to 3306>",
    "masterarn": "<required: the ARN of the master secret used to create 2nd user and change passwords>"
  }
  ```   [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMariaDBRotationMultiUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMariaDBRotationMultiUser/lambda_function.py)     RDS MySQL Single User  

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSMySQLRotationSingleUser
```   **Name:** SecretsManagerRDSMySQLRotationSingleUser   **Supported database/service:** MySQL database that's hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.   **Rotation strategy:** This changes the password for a user whose credentials are stored in the secret that's rotated\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)\.   **Expected `SecretString` structure:**  

  ```
  {
    "engine": "mysql",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to None>",
    "port": "<optional: TCP port number. If not specified, defaults to 3306>"
  }
  ```   [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMySQLRotationSingleUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMySQLRotationSingleUser/lambda_function.py)     RDS MySQL Master User  

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSMySQLRotationMultiUser
```   **Name:** SecretsManagerRDSMySQLRotationMultiUser   **Supported database/service:** MySQL database that's hosted on an Amazon RDS database instance\.   **Rotation strategy:** Two users are alternated during rotation by using the credentials of a separate master user, which is stored in a separate secret\. The user that's not currently active has its password changed before it's made the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.   **Expected `SecretString` structure:**  

  ```
  {
    "engine": "mysql",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to None>",
    "port": "<optional: TCP port number. If not specified, defaults to 3306>",
    "masterarn": "<required: the ARN of the master secret used to create 2nd user and change passwords>"
  }
  ```   [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMySQLRotationMultiUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMySQLRotationMultiUser/lambda_function.py)     RDS Oracle Single User  

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSOracleRotationSingleUser
```   **Name:** SecretsManagerRDSOracleRotationSingleUser   **Supported database/service:** Oracle database that's hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.   **Rotation strategy:** This changes the password for a user whose credentials are stored in the secret that's rotated\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)\.   **Expected `SecretString` structure:**  

  ```
  {
    "engine": "oracle",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<required: database name>",
    "port": "<optional: TCP port number. If not specified, defaults to 1521>"
  }
  ```   [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSOracleRotationSIngleUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSOracleRotationSIngleUser/lambda_function.py)     RDS Oracle Master User  

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSOracleRotationMultiUser
```   **Name:** SecretsManagerRDSOracleRotationMultiUser   **Supported database/service:** Oracle database that's hosted on an Amazon RDS database instance\.   **Rotation strategy:** Two users are alternated during rotation by using the credentials of a separate master user, which is stored in a separate secret\. The user that's not currently active has its password changed before it's made the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.   **Expected `SecretString` structure:**  

  ```
  {
    "engine": "oracle",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<required: database name>",
    "port": "<optional: TCP port number. If not specified, defaults to 1521>",
    "masterarn": "<required: the ARN of the master secret used to create 2nd user and change passwords>"
  }
  ```   [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSOracleRotationMultiUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSOracleRotationMultiUser/lambda_function.py)     RDS PostgreSQL Single User  

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSPostgreSQLRotationSingleUser
```   **Name:** SecretsManagerRDSPostgreSQLRotationSingleUser   **Supported database/service:** PostgreSQL database that's hosted on an Amazon RDS database instance\.   **Rotation strategy:** This changes the password for a user whose credentials are stored in the secret that's rotated\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)\.   **Expected `SecretString` structure:**  

  ```
  {
    "engine": "postgres",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to 'postgres'>",
    "port": "<optional: TCP port number. If not specified, defaults to 5432>"
  }
  ```   [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationSingleUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationSingleUser/lambda_function.py)     RDS PostgreSQL Master User  

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSPostgreSQLRotationMultiUser
```   **Name:** SecretsManagerRDSPostgreSQLRotationMultiUser   **Supported database/service:** PostgreSQL database that's hosted on an Amazon RDS database instance\.   **Rotation strategy:** Two users are alternated during rotation by using the credentials of a separate master user, which is stored in a separate secret\. The user that's not currently active has its password changed before it's made the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.   **Expected `SecretString` structure:**  

  ```
  {
    "engine": "postgres",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to 'postgres'>",
    "port": "<optional: TCP port number. If not specified, defaults to 5432>",
    "masterarn": "<required: the ARN of the master secret used to create 2nd user and change passwords>"
  }
  ```   [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationMultiUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationMultiUser/lambda_function.py)     RDS Microsoft SQLServer Single User  

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSSQLServerRotationSingleUser
```   **Name:** SecretsManagerRDSSQLServerRotationSingleUser   **Supported database/service:** Microsoft SQLServer database that's hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.   **Rotation strategy:** This changes the password for a user whose credentials are stored in the secret that's rotated\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)\.   **Expected `SecretString` structure:**  

  ```
  {
    "engine": "sqlserver",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to 'master'>",
    "port": "<optional: TCP port number. If not specified, defaults to 1433>"
  }
  ```   [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSSQLServerRotationSingleUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSSQLServerRotationSingleUser/lambda_function.py)     RDS Microsoft SQLServer Master User  

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSSQLServerRotationMultiUser
```   **Name:** SecretsManagerRDSSQLServerRotationMultiUser   **Supported database/service:** Microsoft SQLServer database that's hosted on an Amazon RDS database instance\.   **Rotation strategy:** Two users are alternated during rotation by using the credentials of a separate master user, which is stored in a separate secret\. The user that's not currently active has its password changed before it's made the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.   **Expected `SecretString` structure:**  

  ```
  {
    "engine": "sqlserver",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to 'master'>",
    "port": "<optional: TCP port number. If not specified, defaults to 1433>",
    "masterarn": "<required: the ARN of the master secret used to create 2nd user and change passwords>"
  }
  ```   [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSSQLServerRotationMultiUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSSQLServerRotationMultiUser/lambda_function.py)       Templates for Other Services    Generic Rotation Function Template  

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRotationTemplate
```   **Name:** SecretsManagerRotationTemplate   **Supported database/service:** None\. You supply the code to interact with whatever service you want\.   **Rotation strategy:** None\. You supply the code to implement whatever rotation strategy you want\. For more information about customizing your own function, see [Understanding and Customizing Your Lambda Rotation Function](rotating-secrets-lambda-function-customizing.md)\.   **Expected `SecretString` structure:** You define this as part of the code that you write\.   **Source code:** [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRotationTemplate/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRotationTemplate/lambda_function.py)     ](reference_available-rotation-templates.md)\.

**Note**  
`UpdateSecret` automatically moves the staging label `AWSCURRENT` to the new version of the secret\.   
`PutSecretValue` *does not* automatically move staging labels\. However, it does add `AWSCURRENT` if this command creates the first version of the secret\. Otherwise, it only attaches or moves those labels that you explicitly request with the `VersionStages` parameter\.  
Any time the staging label `AWSCURRENT` moves from one version to another, Secrets Manager automatically moves the staging label `AWSPREVIOUS` to the version that `AWSCURRENT` was just removed from\.
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html), [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutSecretValue.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutSecretValue.html)
+ **AWS CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html), [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/put-secret-value.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/put-secret-value.html)

**Example**  
The following example AWS CLI command changes the secret value for a secret\. This results in the creation of a new version\. Secrets Manager automatically moves the `AWSCURRENT` staging label to the new version\. Also, the `AWSPREVIOUS` staging label is automatically moved to the older version that `AWSCURRENT` was just removed from\.  

```
$ aws secretsmanager update-secret --secret-id production/MyAwesomeAppSecret --secret-string '{"username":"anika","password":"a different password"}'
{
    "SecretARN": "arn:aws:secretsmanager:us-west-2:123456789012:secret:production/MyAwesomeAppSecret-AbCdEf",
    "SecretName": "production/MyAwesomeAppSecret",
    "SecretVersionId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE"
}
```
The `SecretVersionId` in the output is the unique secret version ID of the new version of the secret\. You can manually provide the value by using the `ClientRequestToken` parameter\. If you don't specify the value, the SDK or AWS CLI generates a random UUID value for you\.

------<a name="proc-description"></a>

**To modify the description of a secret**  
Follow the steps under one of the following tabs:

------
#### [ Using the console ]<a name="proc-description-console"></a>

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the list of secrets, choose the name of the secret that you want to modify\.

1. In the **Secrets details** section, choose **Actions**, and then choose **Edit description**\.

1. Enter a new description or edit the existing text, and then choose **Save**\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-description-api"></a>

You can use the following commands to modify the description of a secret in AWS Secrets Manager:
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html)
+ **AWS CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html)

**Example**  
The following example AWS CLI command adds or replaces the description with the one provided by the `--description` parameter\.  

```
$ aws secretsmanager update-secret --secret-id production/MyAwesomeAppSecret --description 'This is the description I want to attach to the secret.'
{
    "SecretARN": "arn:aws:secretsmanager:region:accountid:secret:production/MyAwesomeAppSecret-AbCdEf",
    "SecretName": "production/MyAwesomeAppSecret",
    "SecretVersionId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE"
}
```

------<a name="proc-kmskey"></a>

**To modify the AWS KMS encryption key used by a secret**  
Follow the steps under one of the following tabs:

------
#### [ Using the console ]<a name="proc-kmskey-console"></a>

**Important**  
If you change the encryption key that's used by a secret, you must update the secret value \(with UpdateSecret or PutSecretValue\) at least once before you disable or delete the first CMK\. Updating the secret value decrypts it using the old CMK and re\-encrypts it with the new CMK\. If you disable or delete the first CMK before this update, the key cannot be decrypted and you lose the contents of the secret unless you can re\-enable the CMK\.

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the list of secrets, choose the name of the secret that you want to modify\.

1. In the **Secrets details** section, choose **Actions**, and then choose **Edit encryption key**\.

1. Choose the AWS KMS encryption key that you want to use to encrypt and decrypt later versions of your secret\. Then choose **Save**\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-kmskey-api"></a>

**Important**  
If you change the encryption key that's used by a secret, you must update the secret value \(with UpdateSecret or PutSecretValue\) at least once before you disable or delete the first CMK\. Updating the secret value decrypts it using the old CMK and reencrypts it with the new CMK\. If you disable or delete the first CMK before this update, the key cannot be decrypted and you lose the contents of the secret unless you can re\-enable the CMK\.

You can use the following commands to modify the AWS KMS encryption key that's used by the secret\. You must specify the CMK by its Amazon Resource Name \(ARN\)\.
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecret.html)
+ **AWS CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret.html)

**Example**  
The following example AWS CLI command adds or replaces the AWS KMS CMK that's used for all encryption and decryption operations in this secret from this time on\.  

```
$ aws secretsmanager update-secret --secret-id production/MyAwesomeAppSecret --kms-key-id arn:aws:kms:region:123456789012:key/EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE
```

------<a name="proc-tags"></a>

**To modify the tags attached to a secret**  
Follow the steps under one of the following tabs:

------
#### [ Using the console ]<a name="proc-tags-console"></a>

Tag key names and values are case sensitive\. Only one tag on a secret can have a given key name\.

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the list of secrets, choose the name of the secret that you want to modify\.

1. In the **Tags** section, choose **Edit**\.

1. Any existing tags are displayed\. You can type over any of the key names or values\.

1. You can remove any existing row by choosing **Remove tag** to the right of that row\.

1. If you need to add another key\-value pair, choose **Add tag**, and then enter your new key name and the associated value\.

1. When you're done making changes, choose **Save**\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-tags-api"></a>

You can use the following commands to add or remove the tags that are attached to a secret in Secrets Manager\. Key names and values are case sensitive\. Only one tag on a secret can have a given key name\. To edit an existing tag, add a tag with the same key name but with a different value\. That doesn't add a new key\-value pair\. Instead, it updates the value in the existing pair\. To change a key name, you must remove the first key and add a second with the new name\.
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_TagResource.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_TagResource.html), [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UntagResource.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UntagResource.html)
+ **AWS CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/tag-resource.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/tag-resource.html), [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/untag-resource.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/untag-resource.html)

**Example**  
The following example AWS CLI command adds or replaces the tags with those provided by the `--tags` parameter\. The parameter is expected to be a JSON array of `Key` and `Value` elements:  

```
$ aws secretsmanager tag-resource --secret-id MySecret2 --tags '[{"Key":"costcenter","Value":"12345"},{"Key":"environment","Value":"production"}]'
```
The tag\-resource command doesn't return any output\. 

**Example**  
The following example AWS CLI command removes the tags with the key "environment" from the specified secret:  

```
$ aws secretsmanager untag-resource --secret-id MySecret2 --tag-keys 'environment'
```
The tag\-resource command doesn't return any output\. 

------