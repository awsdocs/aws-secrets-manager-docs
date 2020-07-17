# AWS Templates You Can Use to Create Lambda Rotation Functions<a name="reference_available-rotation-templates"></a>

This section identifies the AWS managed templates you can use to create a Lambda rotation function for your AWS Secrets Manager secret\. These templates associate with the AWS Serverless Application Repository, which uses AWS CloudFormation to create 'stacks' of preconfigured resources\. In this case, the templates create a stack that consists of the Lambda function and an IAM role that Secrets Manager can assume to invoke the function when rotation occurs\.

To create a Lambda rotation function with any of the following templates, you can copy and paste the ARN of the specified template into the CLI commands described in the topic [Rotating AWS Secrets Manager Secrets for Other Databases or Services](rotating-secrets-create-generic-template.md)\.

Each of the following templates creates a Lambda rotation function for a different combination of database and rotation strategy\. The first bullet under each shows the database or service supported by the function\. The second bullet describes the rotation strategy implemented by the function\. The third bullet specifies the JSON structure the rotation function expects to find in the `SecretString` value of the rotated secret\.

**RDS databases**
+ [RDS MariaDB Single User](#sar-template-mariadb-singleuser)
+ [RDS MariaDB Master User](#sar-template-mariadb-multiuser)
+ [RDS MySQL Single User](#sar-template-mysql-singleuser)
+ [RDS MySQL Multiple Users](#sar-template-mysql-multiuser)
+ [RDS Oracle Single User](#sar-template-oracle-singleuser)
+ [RDS Oracle Master User](#sar-template-oracle-multiuser)
+ [RDS PostgreSQL Single User](#sar-template-postgre-singleuser)
+ [RDS PostgreSQL Master User](#sar-template-postgre-multiuser)
+ [RDS Microsoft SQLServer Single User](#sar-template-sqlserver-singleuser)
+ [RDS Microsoft SQLServer Master User](#sar-template-sqlserver-multiuser)

**Other databases and services**
+ [MongoDB Single User](#sar-template-mongodb-singleuser)
+ [MongoDB Master User](#sar-template-mongodb-multiuser)
+ [Amazon Redshift Single User](#sar-template-redshift-singleuser)
+ [Amazon Redshift Master User](#sar-template-redshift-multiuser)
+ [Generic Rotation Function Template](#sar-template-generic)

## Templates for Amazon RDS Databases<a name="RDS_rotation_templates"></a>

### RDS MariaDB Single User<a name="sar-template-mariadb-singleuser"></a>

```
arn:aws:serverlessrepo:us-east-1:1234456789012:applications/SecretsManagerRDSMariaDBRotationSingleUser
```
+ **Name:** SecretsManagerRDSMariaDBRotationSingleUser
+ **Supported database/service:** MariaDB database hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.
+ **Rotation strategy:** This changes the password for a user with credentials stored in the rotated secret\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)\.
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
+ [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMariaDBRotationSingleUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMariaDBRotationSingleUser/lambda_function.py)

### RDS MariaDB Master User<a name="sar-template-mariadb-multiuser"></a>

```
arn:aws:serverlessrepo:us-east-1:123456789012:applications/SecretsManagerRDSMariaDBRotationMultiUser
```
+ **Name:** SecretsManagerRDSMariaDBRotationMultiUser
+ **Supported database/service:** MariaDB database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** Two users alternate during rotation by using the credentials of a separate master user, stored in a separate secret\. Secrets Manager changes the password of the inactive user before the user becomes the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.
+ **Expected `SecretString` structure:** 

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
  ```
+ [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMariaDBRotationMultiUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMariaDBRotationMultiUser/lambda_function.py)

### RDS MySQL Single User<a name="sar-template-mysql-singleuser"></a>

```
arn:aws:serverlessrepo:us-east-1:123456789012:applications/SecretsManagerRDSMySQLRotationSingleUser
```
+ **Name:** SecretsManagerRDSMySQLRotationSingleUser
+ **Supported database/service:** MySQL database hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.
+ **Rotation strategy:** This changes the password for a user with credentials stored in the rotated secret\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)\.
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
+ [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMySQLRotationSingleUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMySQLRotationSingleUser/lambda_function.py)

### RDS MySQL Multiple Users<a name="sar-template-mysql-multiuser"></a>

```
arn:aws:serverlessrepo:us-east-1:123456789012:applications/SecretsManagerRDSMySQLRotationMultiUser
```
+ **Name:** SecretsManagerRDSMySQLRotationMultiUser
+ **Supported database/service:** MySQL database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** Two users alternate during rotation by using the credentials of a separate master user, stored in a separate secret\. Secrets Manager changes the password of the inactive user before the user becomes the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.
+ **Expected `SecretString` structure:** 

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
  ```
+ [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMySQLRotationMultiUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMySQLRotationMultiUser/lambda_function.py)

### RDS Oracle Single User<a name="sar-template-oracle-singleuser"></a>

```
arn:aws:serverlessrepo:us-east-1:123456789012:applications/SecretsManagerRDSOracleRotationSingleUser
```
+ **Name:** SecretsManagerRDSOracleRotationSingleUser
+ **Supported database/service:** Oracle database hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.
+ **Rotation strategy:** This changes the password for a user with credentials stored in the rotated secret\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)\.
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
+ [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSOracleRotationSIngleUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSOracleRotationSIngleUser/lambda_function.py)

### RDS Oracle Master User<a name="sar-template-oracle-multiuser"></a>

```
arn:aws:serverlessrepo:us-east-1:123456789012:applications/SecretsManagerRDSOracleRotationMultiUser
```
+ **Name:** SecretsManagerRDSOracleRotationMultiUser
+ **Supported database/service:** Oracle database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** Two users alternate during rotation by using the credentials of a separate master user, stored in a separate secret\. Secrets Manager changes the password of the inactive user before the user becomes the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.
+ **Expected `SecretString` structure:** 

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
  ```
+ [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSOracleRotationMultiUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSOracleRotationMultiUser/lambda_function.py)

### RDS PostgreSQL Single User<a name="sar-template-postgre-singleuser"></a>

```
arn:aws:serverlessrepo:us-east-1:123456789012:applications/SecretsManagerRDSPostgreSQLRotationSingleUser
```
+ **Name:** SecretsManagerRDSPostgreSQLRotationSingleUser
+ **Supported database/service:** PostgreSQL database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** This changes the password for a user with credentials stored in the rotated secret\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)\.
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
+ [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationSingleUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationSingleUser/lambda_function.py)

### RDS PostgreSQL Master User<a name="sar-template-postgre-multiuser"></a>

```
arn:aws:serverlessrepo:us-east-1:123456789012:applications/SecretsManagerRDSPostgreSQLRotationMultiUser
```
+ **Name:** SecretsManagerRDSPostgreSQLRotationMultiUser
+ **Supported database/service:** PostgreSQL database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** Two users alternate during rotation by using the credentials of a separate master user, stored in a separate secret\. Secrets Manager changes the password of the inactive user before the user becomes the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.
+ **Expected `SecretString` structure:** 

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
  ```
+ [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationMultiUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationMultiUser/lambda_function.py)

### RDS Microsoft SQLServer Single User<a name="sar-template-sqlserver-singleuser"></a>

```
arn:aws:serverlessrepo:us-east-1:123456789012:applications/SecretsManagerRDSSQLServerRotationSingleUser
```
+ **Name:** SecretsManagerRDSSQLServerRotationSingleUser
+ **Supported database/service:** Microsoft SQLServer database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** This changes the password for a user with credentials stored in the rotated secret\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)\.
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
+ [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSSQLServerRotationSingleUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSSQLServerRotationSingleUser/lambda_function.py)

### RDS Microsoft SQLServer Master User<a name="sar-template-sqlserver-multiuser"></a>

```
arn:aws:serverlessrepo:us-east-1:123456789012:applications/SecretsManagerRDSSQLServerRotationMultiUser
```
+ **Name:** SecretsManagerRDSSQLServerRotationMultiUser
+ **Supported database/service:** Microsoft SQLServer database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** Two users alternate during rotation by using the credentials of a separate master user, stored in a separate secret\. Secrets Manager changes the password of the inactive user before the user becomes the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.
+ **Expected `SecretString` structure:** 

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
  ```
+ [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSSQLServerRotationMultiUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSSQLServerRotationMultiUser/lambda_function.py)

## Templates for Other Databases<a name="NON-RDS_rotation_templates"></a>

### MongoDB Single User<a name="sar-template-mongodb-singleuser"></a>

```
arn:aws:serverlessrepo:us-east-1:123456789012:applications/SecretsManagerMongoDBRotationSingleUser
```
+ **Name:** SecretsManagerMongoDBRotationSingleUser
+ **Supported database/service:** MongoDB database version 3\.2 or 3\.4\.
+ **Rotation strategy:** This changes the password for a user with credentials stored in the rotated secret\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)\.
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
+ **Source code:** [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerMongoDBRotationSingleUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerMongoDBRotationSingleUser/lambda_function.py)

### MongoDB Master User<a name="sar-template-mongodb-multiuser"></a>

```
arn:aws:serverlessrepo:us-east-1:123456789012:applications/SecretsManagerMongoDBRotationMultiUser
```
+ **Name:** SecretsManagerMongoDBRotationMultiUser
+ **Supported database or service:** MongoDB database version 3\.2 or 3\.4\.
+ **Rotation strategy:** Two users alternate during rotation by using the credentials of a separate master user, and stored in a separate secret\.Secrets Manager changes the password of the inactive user before the user becomes the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "engine": "mongo",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to None>",
    "port": "<optional: TCP port number. If not specified, defaults to 27017>",
    "masterarn": "<required: the ARN of the master secret used to create 2nd user and change passwords>"
  }
  ```
+  [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerMongoDBRotationMultiUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerMongoDBRotationMultiUser/lambda_function.py)

### Amazon Redshift Single User<a name="sar-template-redshift-singleuser"></a>

```
arn:aws:serverlessrepo:us-east-1:123456789012:applications/SecretsManagerRedShiftRotationSingleUser
```
+ **Name:** SecretsManagerRedShiftRotationSingleUser
+ **Supported database/service:** Amazon Redshift
+ **Rotation strategy:** This changes the password for a user with credentials stored in the rotated secret\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)\.
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
+  [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRedshiftRotationSingleUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRedshiftRotationSingleUser/lambda_function.py)

### Amazon Redshift Master User<a name="sar-template-redshift-multiuser"></a>

```
arn:aws:serverlessrepo:us-east-1:123456789012:applications/SecretsManagerRedShiftRotationMultiUser
```
+ **Name:** SecretsManagerRedShiftRotationMultiUser
+ **Supported database/service:** Amazon Redshift
+ **Rotation strategy:** Two users alternate during rotation by using the credentials of a separate master user, stored in a separate secret\. Secrets Manager changes the password of the inactive user before becomeing the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "engine": "redshift",
    "host": "<required: instance host name/resolvable DNS name>",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to None>",
    "port": "<optional: TCP port number. If not specified, defaults to ?????>",
    "masterarn": "<required: the ARN of the master secret used to create 2nd user and change passwords>"
  }
  ```
+  [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRedshiftRotationMultiUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRedshiftRotationMultiUser/lambda_function.py)

## Templates for Other Services<a name="OTHER_rotation_templates"></a>

### Generic Rotation Function Template<a name="sar-template-generic"></a>

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRotationTemplate
```
+ **Name:** SecretsManagerRotationTemplate
+ **Supported database/service:** None\. You supply the code to interact with whatever service you want\.
+ **Rotation strategy:** None\. You supply the code to implement whatever rotation strategy you want\. For more information about customizing your own function, see [Understanding and Customizing Your Lambda Rotation Function](rotating-secrets-lambda-function-customizing.md)\.
+ **Expected `SecretString` structure:** You define this as part of the code that you write\.
+ **Source code:** [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRotationTemplate/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRotationTemplate/lambda_function.py)