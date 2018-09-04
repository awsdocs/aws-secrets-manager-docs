# AWS Templates You Can Use to Create Lambda Rotation Functions<a name="reference_available-rotation-templates"></a>

This section identifies the AWS managed templates that you can use to create a Lambda rotation function for your AWS Secrets Manager secret\. These templates are associated with the AWS Serverless Application Repository, which uses AWS CloudFormation to create 'stacks' of preconfigured resources\. In this case, they create a stack that consists of the Lambda function and an IAM role that Secrets Manager can assume to invoke the function when rotation occurs\.

**To create a Lambda rotation function with any of the following templates, you can copy and paste the ARN of the specified template into the CLI commands described in the topic [Rotating AWS Secrets Manager Secrets for Other Databases or Services](rotating-secrets-create-generic-template.md)\.**

Each of the following templates creates a Lambda rotation function for a different combination of database and rotation strategy\. The first bullet under each shows the database or service that the function supports\. The second bullet describes the rotation strategy that's implemented by the function\. The third bullet specifies the JSON structure that the rotation function expects to find in the `SecretString` value of the secret being rotated\.

**RDS databases**
+ [RDS MariaDB Single User](#sar-template-mariadb-singleuser)
+ [RDS MariaDB Master User](#sar-template-mariadb-multiuser)
+ [RDS MySQL Single User](#sar-template-mysql-singleuser)
+ [RDS MySQL Master User](#sar-template-mysql-multiuser)
+ [RDS Oracle Single User](#sar-template-oracle-singleuser)
+ [RDS Oracle Master User](#sar-template-oracle-multiuser)
+ [RDS PostgreSQL Single User](#sar-template-postgre-singleuser)
+ [RDS PostgreSQL Master User](#sar-template-postgre-multiuser)
+ [RDS Microsoft SQLServer Single User](#sar-template-sqlserver-singleuser)
+ [RDS Microsoft SQLServer Master User](#sar-template-sqlserver-multiuser)

**Other databases and services**
+ [Generic Rotation Function Template](#sar-template-generic)

## Templates for Databases Running on Amazon RDS<a name="RDS_rotation_templates"></a>

### RDS MariaDB Single User<a name="sar-template-mariadb-singleuser"></a>

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSMariaDBRotationSingleUser
```
+ **Name:** SecretsManagerRDSMariaDBRotationSingleUser
+ **Supported database/service:** MariaDB database that's hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.
+ **Rotation strategy:** This changes the password for a user whose credentials are stored in the secret that's rotated\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)\.
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
+ **Source code:** [Secrets Manager Lambda Rotation Template: RDS MariaDB Single User](reference_template_MariaDB_SingleUser.md)

### RDS MariaDB Master User<a name="sar-template-mariadb-multiuser"></a>

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSMariaDBRotationMultiUser
```
+ **Name:** SecretsManagerRDSMariaDBRotationMultiUser
+ **Supported database/service:** MariaDB database that's hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** Two users are alternated during rotation by using the credentials of a separate master user, which is stored in a separate secret\. The user that's not currently active has its password changed before it's made the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.
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
+ **Source code:** [Secrets Manager Lambda Rotation Template: RDS MariaDB Multiple User](reference_template_MariaDB_MultiUser.md)

### RDS MySQL Single User<a name="sar-template-mysql-singleuser"></a>

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSMySQLRotationSingleUser
```
+ **Name:** SecretsManagerRDSMySQLRotationSingleUser
+ **Supported database/service:** MySQL database that's hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.
+ **Rotation strategy:** This changes the password for a user whose credentials are stored in the secret that's rotated\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)\.
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
+ **Source code:** [Secrets Manager Lambda Rotation Template: RDS MySQL Single User](reference_template_MySql_SingleUser.md)

### RDS MySQL Master User<a name="sar-template-mysql-multiuser"></a>

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSMySQLRotationMultiUser
```
+ **Name:** SecretsManagerRDSMySQLRotationMultiUser
+ **Supported database/service:** MySQL database that's hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** Two users are alternated during rotation by using the credentials of a separate master user, which is stored in a separate secret\. The user that's not currently active has its password changed before it's made the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.
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
+ **Source code:** [Secrets Manager Lambda Rotation Template: RDS MySQL Multiple User](reference_template_MySql_MultiUser.md)

### RDS Oracle Single User<a name="sar-template-oracle-singleuser"></a>

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSOracleRotationSingleUser
```
+ **Name:** SecretsManagerRDSOracleRotationSingleUser
+ **Supported database/service:** Oracle database that's hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.
+ **Rotation strategy:** This changes the password for a user whose credentials are stored in the secret that's rotated\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)\.
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
+ **Source code:** [Secrets Manager Lambda Rotation Template: RDS Oracle Single User](reference_template_Oracle_SingleUser.md)

### RDS Oracle Master User<a name="sar-template-oracle-multiuser"></a>

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSOracleRotationMultiUser
```
+ **Name:** SecretsManagerRDSOracleRotationMultiUser
+ **Supported database/service:** Oracle database that's hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** Two users are alternated during rotation by using the credentials of a separate master user, which is stored in a separate secret\. The user that's not currently active has its password changed before it's made the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.
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
+ **Source code:** [Secrets Manager Lambda Rotation Template: RDS Oracle Multiple User](reference_template_Oracle_MultiUser.md)

### RDS PostgreSQL Single User<a name="sar-template-postgre-singleuser"></a>

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSPostgreSQLRotationSingleUser
```
+ **Name:** SecretsManagerRDSPostgreSQLRotationSingleUser
+ **Supported database/service:** PostgreSQL database that's hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** This changes the password for a user whose credentials are stored in the secret that's rotated\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)\.
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
+ **Source code:** [Secrets Manager Lambda Rotation Template: RDS PostgreSQL Single User](reference_template_PostgreSql_SingleUser.md)

### RDS PostgreSQL Master User<a name="sar-template-postgre-multiuser"></a>

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSPostgreSQLRotationMultiUser
```
+ **Name:** SecretsManagerRDSPostgreSQLRotationMultiUser
+ **Supported database/service:** PostgreSQL database that's hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** Two users are alternated during rotation by using the credentials of a separate master user, which is stored in a separate secret\. The user that's not currently active has its password changed before it's made the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.
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
+ **Source code:** [Secrets Manager Lambda Rotation Template: RDS PostgreSQL Multiple User](reference_template_PostgreSql_MultiUser.md)

### RDS Microsoft SQLServer Single User<a name="sar-template-sqlserver-singleuser"></a>

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSSQLServerRotationSingleUser
```
+ **Name:** SecretsManagerRDSSQLServerRotationSingleUser
+ **Supported database/service:** Microsoft SQLServer database that's hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.
+ **Rotation strategy:** This changes the password for a user whose credentials are stored in the secret that's rotated\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)\.
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
+ **Source code:** [Secrets Manager Lambda Rotation Template: RDS SQLServer Single User](reference_template_SQLServer_SingleUser.md)

### RDS Microsoft SQLServer Master User<a name="sar-template-sqlserver-multiuser"></a>

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSSQLServerRotationMultiUser
```
+ **Name:** SecretsManagerRDSSQLServerRotationMultiUser
+ **Supported database/service:** Microsoft SQLServer database that's hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** Two users are alternated during rotation by using the credentials of a separate master user, which is stored in a separate secret\. The user that's not currently active has its password changed before it's made the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.
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
+ **Source code:** [Secrets Manager Lambda Rotation Template: RDS SQLServer Multiple User](reference_template_SQLServer_MultiUser.md)

## Templates for Other Services<a name="OTHER_rotation_templates"></a>

### Generic Rotation Function Template<a name="sar-template-generic"></a>

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRotationTemplate
```
+ **Name:** SecretsManagerRotationTemplate
+ **Supported database/service:** None\. You supply the code to interact with whatever service you want\.
+ **Rotation strategy:** None\. You supply the code to implement whatever rotation strategy you want\. For more information about customizing your own function, see [Understanding and Customizing Your Lambda Rotation Function](rotating-secrets-lambda-function-customizing.md)\.
+ **Expected `SecretString` structure:** You define this as part of the code that you write\.
+ **Source code:** [Secrets Manager Lambda Rotation Template: Generic Template That You Must Customize and Complete](reference_template_Generic.md)