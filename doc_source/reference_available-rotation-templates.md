# AWS Templates You Can Use to Create Lambda Rotation Functions<a name="reference_available-rotation-templates"></a>

This section identifies the AWS managed templates that you can use to create a Lambda rotation function for your AWS Secrets Manager secret\. These templates are associated with the AWS Serverless Application Repository which uses AWS CloudFormation to create 'stacks' of preconfigured resources\. In this case, they create a stack that consists of the Lambda function and an IAM role that Secrets Manager can assume to invoke the function when rotation occurs\.

**To create a Lambda rotation function with any of the following templates, you can copy and paste the ARN of the specified template into the CLI commands described in the topic [Rotating AWS Secrets Manager Secrets for Other Databases or Services](rotating-secrets-create-generic-template.md)\.**

Each template below creates a Lambda rotation function for a different combination of database and rotation strategy\. The first bullet under each shows the database or service that the function supports\. The second bullet describes the rotation strategy that is implemented by the function\. The third bullet specifies the JSON structure that the rotation function expects to find in the `SecretString` value of the secret being rotated\.

## RDS MySQL Single User<a name="sar-template-mysql-singleuser"></a>

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSMySQLRotationSingleUser
```
+ **Name:** SecretsManagerRDSMySQLRotationSingleUser
+ **Supported database/service:** MySQL database hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.
+ **Rotation strategy:** Changes the password for a user whose credentials are stored in the secret that is rotated\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password Only](rotating-secrets-one-user-one-password.md)\.
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

## RDS MySQL Master User<a name="sar-template-mysql-masteruser"></a>

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSMySQLRotationMultiUser
```
+ **Name:** SecretsManagerRDSMySQLRotationMultiUser
+ **Supported database/service:** MySQL database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** Two users are alternated during rotation using the credentials of a separate master user which is stored in a separate secret\. The user that is not currently active has its password changed before it is made the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets By Switching Between Two Existing Users](rotating-secrets-two-users.md)\.
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

## RDS PostgreSQL Single User<a name="sar-template-postgre-singleuser"></a>

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSPostgreSQLRotationSingleUser
```
+ **Name:** SecretsManagerRDSPostgreSQLRotationSingleUser
+ **Supported database/service:** PostgreSQL database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** Changes the password for a user whose credentials are stored in the secret that is rotated\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password Only](rotating-secrets-one-user-one-password.md)\.
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

## RDS PostgreSQL Master User<a name="sar-template-postgre-masteruser"></a>

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRDSPostgreSQLRotationMultiUser
```
+ **Name:** SecretsManagerRDSPostgreSQLRotationMultiUser
+ **Supported database/service:** PostgreSQL database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** Two users are alternated during rotation using the credentials of a separate master user which is stored in a separate secret\. The user that is not currently active has its password changed before it is made the active user\. For more information about this strategy, see [Rotating AWS Secrets Manager Secrets By Switching Between Two Existing Users](rotating-secrets-two-users.md)\.
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
+ **Source code:** [Secrets Manager Lambda Rotation Template: RDS PostgreSQL Single User](reference_template_PostgreSql_MultiUser.md)

## Generic Rotation Function Template<a name="sar-template-generic"></a>

```
arn:aws:serverlessrepo:us-east-1:297356227824:applications/SecretsManagerRotationTemplate
```
+ **Name:** SecretsManagerRotationTemplate
+ **Supported database/service:** None \- You supply the code to interact with whatever service you want\.
+ **Rotation strategy:** None \- You supply the code to implement whatever rotation strategy you want\. For more information about customizing your own function, see [Understanding and Customizing Your Lambda Rotation Function](rotating-secrets-lambda-function-customizing.md)
+ **Expected `SecretString` structure:** You define this as part of the code that you write\.
+ **Source code:** [Secrets Manager Lambda Rotation Template: Generic Template That You Must Customize and Complete](reference_template_Generic.md)