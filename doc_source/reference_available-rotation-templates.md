# AWS Secrets Manager rotation function templates<a name="reference_available-rotation-templates"></a>

**Topics**
+ [Amazon RDS](#RDS_rotation_templates)
+ [Amazon DocumentDB \(with MongoDB compatibility\)](#NON-RDS_rotation_templates)
+ [Amazon Redshift](#template-redshift)
+ [Amazon ElastiCache](#template-ELC)
+ [Other types of secrets](#OTHER_rotation_templates)

 

To use the templates, see:
+ [Rotate Amazon RDS, Amazon Redshift, and Amazon DocumentDB credentials](rotate-secrets_turn-on-for-db.md)
+ [Other types of credentials \(console instructions\)](rotate-secrets_turn-on-for-other.md)
+ [Other types of credentials \(AWS CLI instructions\)](rotate-secrets-cli.md)

The templates support Python 3\.9\. 

## Amazon RDS<a name="RDS_rotation_templates"></a>

**Topics**
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

### Amazon RDS MariaDB single user<a name="sar-template-mariadb-singleuser"></a>
+ **Template name:** SecretsManagerRDSMariaDBRotationSingleUser
+ **Rotation strategy:** [Rotation strategy: single user](getting-started.md#rotating-secrets-one-user-one-password)\.
+ **`SecretString` structure:** [Amazon RDS MariaDB secret structure](reference_secret_json_structure.md#reference_secret_json_structure_rds-maria)\.
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSMariaDBRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMariaDBRotationSingleUser/lambda_function.py)
+ **Dependency: **PyMySQL 1\.0\.2

### Amazon RDS MariaDB alternating users<a name="sar-template-mariadb-multiuser"></a>
+ **Template name:** SecretsManagerRDSMariaDBRotationMultiUser
+ **Rotation strategy:** [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users)\.
+ **`SecretString` structure:** [Amazon RDS MariaDB secret structure](reference_secret_json_structure.md#reference_secret_json_structure_rds-maria)\.
+ **Source code:** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSMariaDBRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMariaDBRotationMultiUser/lambda_function.py)
+ **Dependency: **PyMySQL 1\.0\.2

### Amazon RDS MySQL single user<a name="sar-template-mysql-singleuser"></a>
+ **Template name:** SecretsManagerRDSMySQLRotationSingleUser
+ **Rotation strategy:** [Rotation strategy: single user](getting-started.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** [Amazon RDS MySQL secret structure](reference_secret_json_structure.md#reference_secret_json_structure_rds-mysql)\.
+ **Source code:** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSMySQLRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMySQLRotationSingleUser/lambda_function.py)
+ **Dependency: **PyMySQL 1\.0\.2

### Amazon RDS MySQL alternating users<a name="sar-template-mysql-multiuser"></a>
+ **Template name:** SecretsManagerRDSMySQLRotationMultiUser
+ **Rotation strategy:** [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** [Amazon RDS MySQL secret structure](reference_secret_json_structure.md#reference_secret_json_structure_rds-mysql)\.
+ **Source code: ** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSMySQLRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMySQLRotationMultiUser/lambda_function.py)
+ **Dependency: **PyMySQL 1\.0\.2

### Amazon RDS Oracle single user<a name="sar-template-oracle-singleuser"></a>
+ **Template name:** SecretsManagerRDSOracleRotationSingleUser
+ **Rotation strategy:** [Rotation strategy: single user](getting-started.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** [Amazon RDS Oracle secret structure](reference_secret_json_structure.md#reference_secret_json_structure_rds-oracle)\.
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSOracleRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSOracleRotationSingleUser/lambda_function.py)
+ **Dependency: **cx\_Oracle 6\.0\.2

### Amazon RDS Oracle alternating users<a name="sar-template-oracle-multiuser"></a>
+ **Template name:** SecretsManagerRDSOracleRotationMultiUser
+ **Rotation strategy:** [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** [Amazon RDS Oracle secret structure](reference_secret_json_structure.md#reference_secret_json_structure_rds-oracle)\.
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSOracleRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSOracleRotationMultiUser/lambda_function.py)
+ **Dependency: **cx\_Oracle 6\.0\.2

### Amazon RDS PostgreSQL single user<a name="sar-template-postgre-singleuser"></a>
+ **Template name:** SecretsManagerRDSPostgreSQLRotationSingleUser
+ **Rotation strategy:** [Rotation strategy: single user](getting-started.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** [Amazon RDS PostgreSQL secret structure](reference_secret_json_structure.md#reference_secret_json_structure_rds-postgres)\.
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationSingleUser/lambda_function.py)
+ **Dependency: **PyGreSQL 5\.0\.7

### Amazon RDS PostgreSQL alternating users<a name="sar-template-postgre-multiuser"></a>
+ **Template name:** SecretsManagerRDSPostgreSQLRotationMultiUser
+ **Rotation strategy:** [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** [Amazon RDS PostgreSQL secret structure](reference_secret_json_structure.md#reference_secret_json_structure_rds-postgres)\.
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationMultiUser/lambda_function.py)
+ **Dependency: **PyGreSQL 5\.0\.7

### Amazon RDS Microsoft SQLServer single user<a name="sar-template-sqlserver-singleuser"></a>
+ **Template name:** SecretsManagerRDSSQLServerRotationSingleUser
+ **Rotation strategy:** [Rotation strategy: single user](getting-started.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** [Amazon RDS Microsoft SQLServer secret structure](reference_secret_json_structure.md#reference_secret_json_structure_RDS_sqlserver)\.
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSSQLServerRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSSQLServerRotationSingleUser/lambda_function.py)
+ **Dependency: **Pymssql 2\.2\.2

### Amazon RDS Microsoft SQLServer alternating users<a name="sar-template-sqlserver-multiuser"></a>
+ **Template name:** SecretsManagerRDSSQLServerRotationMultiUser
+ **Rotation strategy:** [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** [Amazon RDS Microsoft SQLServer secret structure](reference_secret_json_structure.md#reference_secret_json_structure_RDS_sqlserver)\.
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSSQLServerRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSSQLServerRotationMultiUser/lambda_function.py)
+ **Dependency: **Pymssql 2\.2\.2

## Amazon DocumentDB \(with MongoDB compatibility\)<a name="NON-RDS_rotation_templates"></a>

### Amazon DocumentDB single user<a name="sar-template-mongodb-singleuser"></a>
+ **Template name:** SecretsManagerMongoDBRotationSingleUser
+ **Rotation strategy:** [Rotation strategy: single user](getting-started.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** [Amazon DocumentDB secret structure](reference_secret_json_structure.md#reference_secret_json_structure_docdb)\.
+ **Source code:** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerMongoDBRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerMongoDBRotationSingleUser/lambda_function.py)
+ **Dependency: **Pymongo 3\.2

### Amazon DocumentDB alternating users<a name="sar-template-mongodb-multiuser"></a>
+ **Template name:** SecretsManagerMongoDBRotationMultiUser
+ **Rotation strategy:** [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** [Amazon DocumentDB secret structure](reference_secret_json_structure.md#reference_secret_json_structure_docdb)\.
+ **Source code: ** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerMongoDBRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerMongoDBRotationMultiUser/lambda_function.py)
+ **Dependency: **Pymongo 3\.2

## Amazon Redshift<a name="template-redshift"></a>

### Amazon Redshift single user<a name="sar-template-redshift-singleuser"></a>
+ **Template name:** SecretsManagerRedshiftRotationSingleUser
+ **Rotation strategy:** [Rotation strategy: single user](getting-started.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** [Amazon Redshift secret structure](reference_secret_json_structure.md#reference_secret_json_structure_RS)\.
+ **Source code: ** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRedshiftRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRedshiftRotationSingleUser/lambda_function.py)
+ **Dependency: **PyGreSQL 5\.0\.7

### Amazon Redshift alternating users<a name="sar-template-redshift-multiuser"></a>
+ **Template name:** SecretsManagerRedshiftRotationMultiUser
+ **Rotation strategy:** [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** [Amazon Redshift secret structure](reference_secret_json_structure.md#reference_secret_json_structure_RS)\.
+ **Source code: ** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRedshiftRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRedshiftRotationMultiUser/lambda_function.py)
+ **Dependency: **PyGreSQL 5\.0\.7

## Amazon ElastiCache<a name="template-ELC"></a>

To use this template, see [Automatically rotating passwords for users](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/User-Secrets-Manager.html) in the *Amazon ElastiCache User Guide*\.
+ **Template name:** SecretsManagerElasticacheUserRotation
+ **Expected `SecretString` structure:** [Amazon ElastiCache secret structure](reference_secret_json_structure.md#reference_secret_json_structure_ELC)\.
+ **Source code: ** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerElasticacheUserRotation/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerElasticacheUserRotation/lambda_function.py)

## Other types of secrets<a name="OTHER_rotation_templates"></a>

Secrets Manager provides this template as a starting point for you to create a rotation function for any type of secret\. For more information, see [Rotate AWS Secrets Manager secrets](rotating-secrets.md)\.
+ **Template name:** SecretsManagerRotationTemplate
+ **Source code: ** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRotationTemplate/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRotationTemplate/lambda_function.py)