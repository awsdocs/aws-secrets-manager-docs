# JSON structure of AWS Secrets Manager secrets<a name="reference_secret_json_structure"></a>

If you want to turn on automatic rotation for a Secrets Manager secret, it must be in the correct JSON structure\. During rotation, Secrets Manager uses the information in the secret to connect to the credential source and update the credentials there\. 

Note that when you use the console to store a database secret, Secrets Manager automatically creates it in the correct JSON structure\.

You can add more key/value pairs to a secret, for example in a database secret, to contain connection information for replica databases in other Regions\.

**Topics**
+ [Amazon RDS MariaDB secret structure](#reference_secret_json_structure_rds-maria)
+ [Amazon RDS MySQL secret structure](#reference_secret_json_structure_rds-mysql)
+ [Amazon RDS Oracle secret structure](#reference_secret_json_structure_rds-oracle)
+ [Amazon RDS PostgreSQL secret structure](#reference_secret_json_structure_rds-postgres)
+ [Amazon RDS Microsoft SQLServer secret structure](#reference_secret_json_structure_RDS_sqlserver)
+ [Amazon DocumentDB secret structure](#reference_secret_json_structure_docdb)
+ [Amazon Redshift secret structure](#reference_secret_json_structure_RS)
+ [Amazon ElastiCache secret structure](#reference_secret_json_structure_ELC)

## Amazon RDS MariaDB secret structure<a name="reference_secret_json_structure_rds-maria"></a>

```
{
    "engine": "mariadb",
    "host": "<instance host name/resolvable DNS name>",
    "username": "<username>",
    "password": "<password>",
    "dbname": "<database name. If not specified, defaults to None>",
    "port": "<TCP port number. If not specified, defaults to 3306>"
}
```

To use the [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users), also include the name\-value pair:

```
    "masterarn": "<the ARN of the elevated secret>"
```

## Amazon RDS MySQL secret structure<a name="reference_secret_json_structure_rds-mysql"></a>

```
{
  "engine": "mysql",
  "host": "<instance host name/resolvable DNS name>",
  "username": "<username>",
  "password": "<password>",
  "dbname": "<database name. If not specified, defaults to None>",
  "port": "<TCP port number. If not specified, defaults to 3306>"
}
```

To use the [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users), also include the name\-value pair:

```
    "masterarn": "<the ARN of the elevated secret>"
```

## Amazon RDS Oracle secret structure<a name="reference_secret_json_structure_rds-oracle"></a>

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

To use the [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users), also include the name\-value pair:

```
    "masterarn": "<the ARN of the elevated secret>"
```

## Amazon RDS PostgreSQL secret structure<a name="reference_secret_json_structure_rds-postgres"></a>

```
{
  "engine": "postgres",
  "host": "<instance host name/resolvable DNS name>",
  "username": "<username>",
  "password": "<password>",
  "dbname": "<database name. If not specified, defaults to 'postgres'>",
  "port": "<TCP port number. If not specified, defaults to 5432>"
}
```

To use the [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users), also include the name\-value pair:

```
    "masterarn": "<the ARN of the elevated secret>"
```

## Amazon RDS Microsoft SQLServer secret structure<a name="reference_secret_json_structure_RDS_sqlserver"></a>

```
{
  "engine": "sqlserver",
  "host": "<instance host name/resolvable DNS name>",
  "username": "<username>",
  "password": "<password>",
  "dbname": "<database name. If not specified, defaults to 'master'>",
  "port": "<TCP port number. If not specified, defaults to 1433>"
}
```

To use the [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users), also include the name\-value pair:

```
    "masterarn": "<the ARN of the elevated secret>"
```

## Amazon DocumentDB secret structure<a name="reference_secret_json_structure_docdb"></a>

```
{
  "engine": "mongo",
  "host": "<instance host name/resolvable DNS name>",
  "username": "<username>",
  "password": "<password>",
  "dbname": "<database name. If not specified, defaults to None>",
  "port": "<TCP port number. If not specified, defaults to 27017>"
}
```

To use the [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users), also include the name\-value pair:

```
    "masterarn": "<the ARN of the elevated secret>"
```

## Amazon Redshift secret structure<a name="reference_secret_json_structure_RS"></a>

```
{
  "engine": "redshift",
  "host": "<instance host name/resolvable DNS name>",
  "username": "<username>",
  "password": "<password>",
  "dbname": "<database name. If not specified, defaults to None>",
  "port": "<TCP port number. If not specified, defaults to 5439>"
}
```

To use the [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users), also include the name\-value pair:

```
    "masterarn": "<the ARN of the elevated secret>"
```

## Amazon ElastiCache secret structure<a name="reference_secret_json_structure_ELC"></a>

```
{
  "password": "<password>",
  "username": "<username>" 
  "user_arn": "ARN of the Amazon EC2 user"
}
```

For more information, see [Automatically rotating passwords for users](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/User-Secrets-Manager.html) in the *Amazon ElastiCache User Guide*\.