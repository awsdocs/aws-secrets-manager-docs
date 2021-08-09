# AWS templates you can use to create Lambda rotation functions<a name="reference_available-rotation-templates"></a>

This section identifies the AWS managed templates you can use to create a Lambda rotation function for your AWS Secrets Manager secret\. These templates associate with the AWS Serverless Application Repository, which uses AWS CloudFormation to create 'stacks' of preconfigured resources\. In this case, the templates create a stack that consists of the Lambda function and an IAM role that Secrets Manager can assume to invoke the function when rotation occurs\.

To create a Lambda rotation function with any of the following templates, you can copy and paste the ARN of the specified template into the CLI commands described in the topic [Rotating AWS Secrets Manager secrets for other databases or services](rotating-secrets-create-generic-template.md)\.

Each of the following templates creates a Lambda rotation function for a different combination of database and rotation strategy\. The first bullet under each shows the database or service supported by the function\. The second bullet describes the rotation strategy implemented by the function\. The third bullet specifies the JSON structure the rotation function expects to find in the `SecretString` value of the rotated secret\.

**RDS databases**
+ [RDS MariaDB single user](#sar-template-mariadb-singleuser)
+ [RDS MariaDB master user](#sar-template-mariadb-multiuser)
+ [RDS MySQL single user](#sar-template-mysql-singleuser)
+ [RDS MySQL multiple users](#sar-template-mysql-multiuser)
+ [RDS Oracle single user](#sar-template-oracle-singleuser)
+ [RDS Oracle master user](#sar-template-oracle-multiuser)
+ [RDS PostgreSQL single user](#sar-template-postgre-singleuser)
+ [RDS PostgreSQL master user](#sar-template-postgre-multiuser)
+ [RDS Microsoft SQLServer single user](#sar-template-sqlserver-singleuser)
+ [RDS Microsoft SQLServer master user](#sar-template-sqlserver-multiuser)

**Other databases and services**
+ [MongoDB single user](#sar-template-mongodb-singleuser)
+ [MongoDB master user](#sar-template-mongodb-multiuser)
+ [Amazon Redshift single user](#sar-template-redshift-singleuser)
+ [Amazon Redshift primary user](#sar-template-redshift-multiuser)
+ [Amazon CloudFront API Key header injection](#sar-template-cloudfront-apikey-header-injection)
+ [Amazon ALB API Key header check](#sar-template-alb-apikey-header-check)
+ [Generic rotation function template](#sar-template-generic)

## Templates for Amazon RDS databases<a name="RDS_rotation_templates"></a>

### RDS MariaDB single user<a name="sar-template-mariadb-singleuser"></a>
+ **Name:** SecretsManagerRDSMariaDBRotationSingleUser
+ **Supported database/service:** MariaDB database hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.
+ **Rotation strategy:** This changes the password for a user with credentials stored in the rotated secret\. For more information about this strategy, see [Rotating AWS Secrets Manager secrets for one user with a single password](rotating-secrets-one-user-one-password.md)\.
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

### RDS MariaDB master user<a name="sar-template-mariadb-multiuser"></a>
+ **Name:** SecretsManagerRDSMariaDBRotationMultiUser
+ **Supported database/service:** MariaDB database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** Two users alternate during rotation by using the credentials of a separate master user, stored in a separate secret\. Secrets Manager changes the password of the inactive user before the user becomes the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager secrets by alternating between two existing users](rotating-secrets-two-users.md)\.
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

### RDS MySQL single user<a name="sar-template-mysql-singleuser"></a>
+ **Name:** SecretsManagerRDSMySQLRotationSingleUser
+ **Supported database/service:** MySQL database hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.
+ **Rotation strategy:** This changes the password for a user with credentials stored in the rotated secret\. For more information about this strategy, see [Rotating AWS Secrets Manager secrets for one user with a single password](rotating-secrets-one-user-one-password.md)\.
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

### RDS MySQL multiple users<a name="sar-template-mysql-multiuser"></a>
+ **Name:** SecretsManagerRDSMySQLRotationMultiUser
+ **Supported database/service:** MySQL database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** Two users alternate during rotation by using the credentials of a separate master user, stored in a separate secret\. Secrets Manager changes the password of the inactive user before the user becomes the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager secrets by alternating between two existing users](rotating-secrets-two-users.md)\.
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

### RDS Oracle single user<a name="sar-template-oracle-singleuser"></a>
+ **Name:** SecretsManagerRDSOracleRotationSingleUser
+ **Supported database/service:** Oracle database hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.
+ **Rotation strategy:** This changes the password for a user with credentials stored in the rotated secret\. For more information about this strategy, see [Rotating AWS Secrets Manager secrets for one user with a single password](rotating-secrets-one-user-one-password.md)\.
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
+ [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSOracleRotationSingleUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSOracleRotationSingleUser/lambda_function.py)

### RDS Oracle master user<a name="sar-template-oracle-multiuser"></a>
+ **Name:** SecretsManagerRDSOracleRotationMultiUser
+ **Supported database/service:** Oracle database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** Two users alternate during rotation by using the credentials of a separate master user, stored in a separate secret\. Secrets Manager changes the password of the inactive user before the user becomes the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager secrets by alternating between two existing users](rotating-secrets-two-users.md)\.
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

### RDS PostgreSQL single user<a name="sar-template-postgre-singleuser"></a>
+ **Name:** SecretsManagerRDSPostgreSQLRotationSingleUser
+ **Supported database/service:** PostgreSQL database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** This changes the password for a user with credentials stored in the rotated secret\. For more information about this strategy, see [Rotating AWS Secrets Manager secrets for one user with a single password](rotating-secrets-one-user-one-password.md)\.
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

### RDS PostgreSQL master user<a name="sar-template-postgre-multiuser"></a>
+ **Name:** SecretsManagerRDSPostgreSQLRotationMultiUser
+ **Supported database/service:** PostgreSQL database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** Two users alternate during rotation by using the credentials of a separate master user, stored in a separate secret\. Secrets Manager changes the password of the inactive user before the user becomes the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager secrets by alternating between two existing users](rotating-secrets-two-users.md)\.
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

### RDS Microsoft SQLServer single user<a name="sar-template-sqlserver-singleuser"></a>
+ **Name:** SecretsManagerRDSSQLServerRotationSingleUser
+ **Supported database/service:** Microsoft SQLServer database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** This changes the password for a user with credentials stored in the rotated secret\. For more information about this strategy, see [Rotating AWS Secrets Manager secrets for one user with a single password](rotating-secrets-one-user-one-password.md)\.
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

### RDS Microsoft SQLServer master user<a name="sar-template-sqlserver-multiuser"></a>
+ **Name:** SecretsManagerRDSSQLServerRotationMultiUser
+ **Supported database/service:** Microsoft SQLServer database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** Two users alternate during rotation by using the credentials of a separate master user, stored in a separate secret\. Secrets Manager changes the password of the inactive user before the user becomes the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager secrets by alternating between two existing users](rotating-secrets-two-users.md)\.
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

## Templates for other databases<a name="NON-RDS_rotation_templates"></a>

### MongoDB single user<a name="sar-template-mongodb-singleuser"></a>
+ **Name:** SecretsManagerMongoDBRotationSingleUser
+ **Supported database/service:** MongoDB database version 3\.2 or 3\.4\.
+ **Rotation strategy:** This changes the password for a user with credentials stored in the rotated secret\. For more information about this strategy, see [Rotating AWS Secrets Manager secrets for one user with a single password](rotating-secrets-one-user-one-password.md)\.
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

### MongoDB master user<a name="sar-template-mongodb-multiuser"></a>
+ **Name:** SecretsManagerMongoDBRotationMultiUser
+ **Supported database or service:** MongoDB database version 3\.2 or 3\.4\.
+ **Rotation strategy:** Two users alternate during rotation by using the credentials of a separate master user, and stored in a separate secret\.Secrets Manager changes the password of the inactive user before the user becomes the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager secrets by alternating between two existing users](rotating-secrets-two-users.md)\.
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

### Amazon Redshift single user<a name="sar-template-redshift-singleuser"></a>

```
arn:aws:serverlessrepo:us-east-1:123456789012:applications/SecretsManagerRDSMySQLRotationSingleUser
```
+ **Name:** SecretsManagerRedshiftRotationSingleUser
+ **Supported database/service:** Amazon Redshift
+ **Rotation strategy:** This changes the password for a user with credentials stored in the rotated secret\. For more information about this strategy, see [Rotating AWS Secrets Manager secrets for one user with a single password](rotating-secrets-one-user-one-password.md)\.
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

### Amazon Redshift primary user<a name="sar-template-redshift-multiuser"></a>
+ **Name:** SecretsManagerRedshiftRotationMultiUser
+ **Supported database/service:** Amazon Redshift
+ **Rotation strategy:** Two users alternate during rotation by using the credentials of a separate primary user, stored in a separate secret\. Secrets Manager changes the password of the inactive user before becoming the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager secrets by alternating between two existing users](rotating-secrets-two-users.md)\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "engine": "redshift",
    "host": "<required: instance host name/resolvable DNS name",
    "username": "<required: username>",
    "password": "<required: password>",
    "dbname": "<optional: database name. If not specified, defaults to None",
    "port": "<optional: TCP port number. If not specified, defaults to 5439",
    "masterarn": "<required: the master secret ARN used to create 2nd user and change passwords"
  }
  ```
+  [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRedshiftRotationMultiUser/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRedshiftRotationMultiUser/lambda_function.py)

## Templates for other services<a name="OTHER_rotation_templates"></a>

### Amazon CloudFront API Key header injection<a name="sar-template-cloudfront-apikey-header-injection"></a>
+ **Name:** SecretsManagerCloudFront
+ **Supported service:** CloudFront\. This Lambda function will add a header to to requests from CloudFront to the backend origin service\.
+ **Rotation strategy:** A secret contains a json string of 3 active key values\. The Lambda Function will pop the oldest key, push a new key, then update a CloudFront distribution to match\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "key1": "<required:string>",
    "key2": "<required:string>",
    "key3": "<required:string>",
  }
  ```

+ **Source code:** [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerCloudFront/](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerCloudFront/)

### Amazon ALB API Key header check<a name="sar-template-alb-apikey-header-check"></a>
+ **Name:** SecretsManagerAlb
+ **Supported service:** Application Load Balancer\. This Lambda function will update the ALB Listener Rules to look for a static API key header\. Unless the header is found, the request will be returned an HTTP403 Access Denied response\.
+ **Rotation strategy:** A secret contains a json string of 3 active key values\. The Lambda Function will pop the oldest key, push a new key, then update ALB Listener Rules to match\.
+ **Expected `SecretString` structure:** 

  ```
  {
    "key1": "<required:string>",
    "key2": "<required:string>",
    "key3": "<required:string>",
  }
  ```

+ **Source code:** [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerCloudFrontAlb/](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerCloudFrontAlb/)

### Generic rotation function template<a name="sar-template-generic"></a>
+ **Name:** SecretsManagerRotationTemplate
+ **Supported database/service:** None\. You supply the code to interact with whatever service you want\.
+ **Rotation strategy:** None\. You supply the code to implement whatever rotation strategy you want\. For more information about customizing your own function, see [Understanding and customizing your Lambda rotation function](rotating-secrets-lambda-function-customizing.md)\.
+ **Expected `SecretString` structure:** You define this as part of your written code\.
+ **Source code:** [https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRotationTemplate/lambda_function.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRotationTemplate/lambda_function.py)