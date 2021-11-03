# Secrets Manager rotation function templates<a name="reference_available-rotation-templates"></a>

To create a Lambda rotation function with any of the following templates, we recommend you use the procedures in [Automatically rotate an Amazon RDS, Amazon DocumentDB, or Amazon Redshift secret](rotate-secrets_turn-on-for-db.md) or [Automatically rotate another type of secret](rotate-secrets_turn-on-for-other.md)\. Secrets Manager includes the required dependencies when you turn on rotation, unless you create your Lambda rotation function by hand\. The templates support Python 3\.7\. 

Secrets Manager provides the following rotation function templates:

**Contents**
+ [Amazon RDS databases](#RDS_rotation_templates)
  + [Amazon RDS MariaDB single user](#sar-template-mariadb-singleuser)
  + [Amazon RDS MariaDB alternating users](#sar-template-mariadb-multiuser)
  + [Amazon RDS MySQL single user](#sar-template-mysql-singleuser)
  + [Amazon RDS MySQL alternating users](#sar-template-mysql-multiuser)
  + [Amazon RDS Oracle single user](#sar-template-oracle-singleuser)
  + [Amazon RDS Oracle alternating users](#sar-template-oracle-multiuser)
  + [Amazon RDS PostgreSQL single user](#sar-template-postgre-singleuser)
  + [Amazon RDS PostgreSQL alternating users](#sar-template-postgre-multiuser)
  + [Amazon RDS Microsoft SQLServer single user](#sar-template-sqlserver-singleuser)
  + [Amazon RDS Microsoft SQLServer alternating users](#sar-template-sqlserver-multiuser)
+ [Amazon DocumentDB databases](#NON-RDS_rotation_templates)
  + [Amazon DocumentDB MongoDB single user](#sar-template-mongodb-singleuser)
  + [Amazon DocumentDB MongoDB alternating users](#sar-template-mongodb-multiuser)
+ [Amazon Redshift](#template-redshift)
  + [Amazon Redshift single user](#sar-template-redshift-singleuser)
  + [Amazon Redshift primary user](#sar-template-redshift-multiuser)
+ [Other types of secrets](#OTHER_rotation_templates)
  + [Generic rotation function template](#sar-template-generic)

## Amazon RDS databases<a name="RDS_rotation_templates"></a>

### Amazon RDS MariaDB single user<a name="sar-template-mariadb-singleuser"></a>
+ **Name:** SecretsManagerRDSMariaDBRotationSingleUser
+ **Supported database/service:** MariaDB database hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.
+ **Rotation strategy:** [Single user rotation strategy](rotating-secrets_strategies.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "engine": "mariadb",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to None>",
    "port": "<optional: TCP port number. If not specified, defaults to 3306>"
  }
  ```
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSMariaDBRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMariaDBRotationSingleUser/lambda_function.py)

### Amazon RDS MariaDB alternating users<a name="sar-template-mariadb-multiuser"></a>
+ **Name:** SecretsManagerRDSMariaDBRotationMultiUser
+ **Supported database/service:** MariaDB database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** [Alternating users rotation strategy](rotating-secrets_strategies.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "engine": "mariadb",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to None>",
    "port": "<optional: TCP port number. If not specified, defaults to 3306>",
    "masterarn": "<required: the ARN of the elevated secret used to create 2nd user and change passwords>"
  }
  ```
+ **Source code:** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSMariaDBRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMariaDBRotationMultiUser/lambda_function.py)

### Amazon RDS MySQL single user<a name="sar-template-mysql-singleuser"></a>
+ **Name:** SecretsManagerRDSMySQLRotationSingleUser
+ **Supported database/service:** MySQL database hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.
+ **Rotation strategy:** [Single user rotation strategy](rotating-secrets_strategies.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "engine": "mysql",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to None>",
    "port": "<optional: TCP port number. If not specified, defaults to 3306>"
  }
  ```
+ **Source code:** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSMySQLRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMySQLRotationSingleUser/lambda_function.py)

### Amazon RDS MySQL alternating users<a name="sar-template-mysql-multiuser"></a>
+ **Name:** SecretsManagerRDSMySQLRotationMultiUser
+ **Supported database/service:** MySQL database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** [Alternating users rotation strategy](rotating-secrets_strategies.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "engine": "mysql",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to None>",
    "port": "<optional: TCP port number. If not specified, defaults to 3306>",
    "masterarn": "<required: the ARN of the elevated secret used to create 2nd user and change passwords>"
  }
  ```
+ **Source code: ** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSMySQLRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMySQLRotationMultiUser/lambda_function.py)

### Amazon RDS Oracle single user<a name="sar-template-oracle-singleuser"></a>
+ **Name:** SecretsManagerRDSOracleRotationSingleUser
+ **Supported database/service:** Oracle database hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.
+ **Rotation strategy:** [Single user rotation strategy](rotating-secrets_strategies.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "engine": "oracle",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<required: database name>",
    "port": "<optional: TCP port number. If not specified, defaults to 1521>"
  }
  ```
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSOracleRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSOracleRotationSingleUser/lambda_function.py)

### Amazon RDS Oracle alternating users<a name="sar-template-oracle-multiuser"></a>
+ **Name:** SecretsManagerRDSOracleRotationMultiUser
+ **Supported database/service:** Oracle database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** [Alternating users rotation strategy](rotating-secrets_strategies.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "engine": "oracle",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<required: database name>",
    "port": "<optional: TCP port number. If not specified, defaults to 1521>",
    "masterarn": "<required: the ARN of the elevated secret used to create 2nd user and change passwords>"
  }
  ```
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSOracleRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSOracleRotationMultiUser/lambda_function.py)

### Amazon RDS PostgreSQL single user<a name="sar-template-postgre-singleuser"></a>
+ **Name:** SecretsManagerRDSPostgreSQLRotationSingleUser
+ **Supported database/service:** PostgreSQL database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** [Single user rotation strategy](rotating-secrets_strategies.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "engine": "postgres",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to 'postgres'>",
    "port": "<optional: TCP port number. If not specified, defaults to 5432>"
  }
  ```
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationSingleUser/lambda_function.py)

### Amazon RDS PostgreSQL alternating users<a name="sar-template-postgre-multiuser"></a>
+ **Name:** SecretsManagerRDSPostgreSQLRotationMultiUser
+ **Supported database/service:** PostgreSQL database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** [Alternating users rotation strategy](rotating-secrets_strategies.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "engine": "postgres",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to 'postgres'>",
    "port": "<optional: TCP port number. If not specified, defaults to 5432>",
    "masterarn": "<required: the ARN of the elevated secret used to create 2nd user and change passwords>"
  }
  ```
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationMultiUser/lambda_function.py)

### Amazon RDS Microsoft SQLServer single user<a name="sar-template-sqlserver-singleuser"></a>
+ **Name:** SecretsManagerRDSSQLServerRotationSingleUser
+ **Supported database/service:** Microsoft SQLServer database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** [Single user rotation strategy](rotating-secrets_strategies.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "engine": "sqlserver",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to 'master'>",
    "port": "<optional: TCP port number. If not specified, defaults to 1433>"
  }
  ```
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSSQLServerRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSSQLServerRotationSingleUser/lambda_function.py)

### Amazon RDS Microsoft SQLServer alternating users<a name="sar-template-sqlserver-multiuser"></a>
+ **Name:** SecretsManagerRDSSQLServerRotationMultiUser
+ **Supported database/service:** Microsoft SQLServer database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** [Alternating users rotation strategy](rotating-secrets_strategies.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "engine": "sqlserver",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to 'master'>",
    "port": "<optional: TCP port number. If not specified, defaults to 1433>",
    "masterarn": "<required: the ARN of the elevated secret used to create 2nd user and change passwords>"
  }
  ```
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSSQLServerRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSSQLServerRotationMultiUser/lambda_function.py)

## Amazon DocumentDB databases<a name="NON-RDS_rotation_templates"></a>

### Amazon DocumentDB MongoDB single user<a name="sar-template-mongodb-singleuser"></a>
+ **Name:** SecretsManagerMongoDBRotationSingleUser
+ **Supported database/service:** MongoDB database version 3\.2 or 3\.4\.
+ **Rotation strategy:** [Single user rotation strategy](rotating-secrets_strategies.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "engine": "mongo",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to None>",
    "port": "<optional: TCP port number. If not specified, defaults to 27017>"
  }
  ```
+ **Source code:** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerMongoDBRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerMongoDBRotationSingleUser/lambda_function.py)

### Amazon DocumentDB MongoDB alternating users<a name="sar-template-mongodb-multiuser"></a>
+ **Name:** SecretsManagerMongoDBRotationMultiUser
+ **Supported database or service:** MongoDB database version 3\.2 or 3\.4\.
+ **Rotation strategy:** [Alternating users rotation strategy](rotating-secrets_strategies.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "engine": "mongo",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to None>",
    "port": "<optional: TCP port number. If not specified, defaults to 27017>",
    "masterarn": "<required: the ARN of the elevated secret used to create 2nd user and change passwords>"
  }
  ```
+ **Source code: ** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerMongoDBRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerMongoDBRotationMultiUser/lambda_function.py)

## Amazon Redshift<a name="template-redshift"></a>

### Amazon Redshift single user<a name="sar-template-redshift-singleuser"></a>

```
arn:aws:serverlessrepo:us-east-2:123456789012:applications/SecretsManagerRDSMySQLRotationSingleUser
```
+ **Name:** SecretsManagerRedshiftRotationSingleUser
+ **Supported database/service:** Amazon Redshift
+ **Rotation strategy:** [Single user rotation strategy](rotating-secrets_strategies.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "engine": "redshift",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to None>",
    "port": "<optional: TCP port number. If not specified, defaults to 5439>"
  }
  ```
+ **Source code: ** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRedshiftRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRedshiftRotationSingleUser/lambda_function.py)

### Amazon Redshift primary user<a name="sar-template-redshift-multiuser"></a>
+ **Name:** SecretsManagerRedshiftRotationMultiUser
+ **Supported database/service:** Amazon Redshift
+ **Rotation strategy:** [Alternating users rotation strategy](rotating-secrets_strategies.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "engine": "redshift",
    "host": "<required: instance host name/resolvable DNS name",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to None",
    "port": "<optional: TCP port number. If not specified, defaults to 5439",
    "masterarn": "<required: the elevated secret ARN used to create 2nd user and change passwords"
  }
  ```
+ **Source code: ** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRedshiftRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRedshiftRotationMultiUser/lambda_function.py)

## Other types of secrets<a name="OTHER_rotation_templates"></a>

### Generic rotation function template<a name="sar-template-generic"></a>
+ **Name:** SecretsManagerRotationTemplate
+ **Supported database/service:** None\. You supply the code to interact with whatever service you want\.
+ **Rotation strategy:** You can use this template to implement your own strategy\. Rotation templates have four steps: [How rotation works](rotate-secrets_how.md)\. To use a rotation function that you created based on this template, see [Automatically rotate another type of secret](rotate-secrets_turn-on-for-other.md)\.
+ **Expected `SecretString` structure:** You define this\.
+ **Source code: ** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRotationTemplate/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRotationTemplate/lambda_function.py)