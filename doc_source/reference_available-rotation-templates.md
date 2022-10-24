# AWS Secrets Manager rotation function templates<a name="reference_available-rotation-templates"></a>

Secrets Manager provides rotation function templates for several types of credentials\. To use the templates, see:
+ [Rotate Amazon RDS, Amazon Redshift, and Amazon DocumentDB credentials](rotate-secrets_turn-on-for-db.md)
+ [Other types of credentials \(console instructions\)](rotate-secrets_turn-on-for-other.md)
+ [Other types of credentials \(AWS CLI instructions\)](rotate-secrets-cli.md)

## Rotation templates<a name="w458aac19c19b5b7"></a>

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
+ [Amazon DocumentDB \(with MongoDB compatibility\) databases](#NON-RDS_rotation_templates)
  + [Amazon DocumentDB single user](#sar-template-mongodb-singleuser)
  + [Amazon DocumentDB alternating users](#sar-template-mongodb-multiuser)
+ [Amazon Redshift](#template-redshift)
  + [Amazon Redshift single user](#sar-template-redshift-singleuser)
  + [Amazon Redshift alternating users](#sar-template-redshift-multiuser)
+ [Other types of secrets](#OTHER_rotation_templates)

The templates support Python 3\.9\. 

### Amazon RDS databases<a name="RDS_rotation_templates"></a>

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

#### Amazon RDS MariaDB single user<a name="sar-template-mariadb-singleuser"></a>
+ **Template name:** SecretsManagerRDSMariaDBRotationSingleUser
+ **Supported database/service:** MariaDB database hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.
+ **Rotation strategy:** [Rotation strategy: single user](getting-started.md#rotating-secrets-one-user-one-password)\.
+ **`SecretString` structure:** [Amazon RDS MariaDB secret structure](reference_secret_json_structure.md#reference_secret_json_structure_rds-maria)\.
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSMariaDBRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMariaDBRotationSingleUser/lambda_function.py)
+ **Dependency: **PyMySQL 1\.0\.2

#### Amazon RDS MariaDB alternating users<a name="sar-template-mariadb-multiuser"></a>
+ **Template name:** SecretsManagerRDSMariaDBRotationMultiUser
+ **Supported database/service:** MariaDB database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users)\.
+ **`SecretString` structure:** [Amazon RDS MariaDB secret structure](reference_secret_json_structure.md#reference_secret_json_structure_rds-maria)\.
+ **Source code:** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSMariaDBRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMariaDBRotationMultiUser/lambda_function.py)
+ **Dependency: **PyMySQL 1\.0\.2

#### Amazon RDS MySQL single user<a name="sar-template-mysql-singleuser"></a>
+ **Template name:** SecretsManagerRDSMySQLRotationSingleUser
+ **Supported database/service:** MySQL database hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.
+ **Rotation strategy:** [Rotation strategy: single user](getting-started.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** [Amazon RDS MySQL secret structure](reference_secret_json_structure.md#reference_secret_json_structure_rds-mysql)\.
+ **Source code:** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSMySQLRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMySQLRotationSingleUser/lambda_function.py)
+ **Dependency: **PyMySQL 1\.0\.2

#### Amazon RDS MySQL alternating users<a name="sar-template-mysql-multiuser"></a>
+ **Template name:** SecretsManagerRDSMySQLRotationMultiUser
+ **Supported database/service:** MySQL database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** [Amazon RDS MySQL secret structure](reference_secret_json_structure.md#reference_secret_json_structure_rds-mysql)\.
+ **Source code: ** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSMySQLRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSMySQLRotationMultiUser/lambda_function.py)
+ **Dependency: **PyMySQL 1\.0\.2

#### Amazon RDS Oracle single user<a name="sar-template-oracle-singleuser"></a>
+ **Template name:** SecretsManagerRDSOracleRotationSingleUser
+ **Supported database/service:** Oracle database hosted on an Amazon Relational Database Service \(Amazon RDS\) database instance\.
+ **Rotation strategy:** [Rotation strategy: single user](getting-started.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** [Amazon RDS Oracle secret structure](reference_secret_json_structure.md#reference_secret_json_structure_rds-oracle)\.
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSOracleRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSOracleRotationSingleUser/lambda_function.py)
+ **Dependency: **cx\_Oracle 6\.0\.2

#### Amazon RDS Oracle alternating users<a name="sar-template-oracle-multiuser"></a>
+ **Template name:** SecretsManagerRDSOracleRotationMultiUser
+ **Supported database/service:** Oracle database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** [Amazon RDS Oracle secret structure](reference_secret_json_structure.md#reference_secret_json_structure_rds-oracle)\.
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSOracleRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSOracleRotationMultiUser/lambda_function.py)
+ **Dependency: **cx\_Oracle 6\.0\.2

#### Amazon RDS PostgreSQL single user<a name="sar-template-postgre-singleuser"></a>
+ **Template name:** SecretsManagerRDSPostgreSQLRotationSingleUser
+ **Supported database/service:** PostgreSQL database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** [Rotation strategy: single user](getting-started.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** [Amazon RDS PostgreSQL secret structure](reference_secret_json_structure.md#reference_secret_json_structure_rds-postgres)\.
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationSingleUser/lambda_function.py)
+ **Dependency: **PyGreSQL 5\.0\.7

#### Amazon RDS PostgreSQL alternating users<a name="sar-template-postgre-multiuser"></a>
+ **Template name:** SecretsManagerRDSPostgreSQLRotationMultiUser
+ **Supported database/service:** PostgreSQL database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** [Amazon RDS PostgreSQL secret structure](reference_secret_json_structure.md#reference_secret_json_structure_rds-postgres)\.
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSPostgreSQLRotationMultiUser/lambda_function.py)
+ **Dependency: **PyGreSQL 5\.0\.7

#### Amazon RDS Microsoft SQLServer single user<a name="sar-template-sqlserver-singleuser"></a>
+ **Template name:** SecretsManagerRDSSQLServerRotationSingleUser
+ **Supported database/service:** Microsoft SQLServer database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** [Rotation strategy: single user](getting-started.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** [Amazon RDS Microsoft SQLServer secret structure](reference_secret_json_structure.md#reference_secret_json_structure_RDS_sqlserver)\.
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSSQLServerRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSSQLServerRotationSingleUser/lambda_function.py)
+ **Dependency: **Pymssql 2\.2\.2

#### Amazon RDS Microsoft SQLServer alternating users<a name="sar-template-sqlserver-multiuser"></a>
+ **Template name:** SecretsManagerRDSSQLServerRotationMultiUser
+ **Supported database/service:** Microsoft SQLServer database hosted on an Amazon RDS database instance\.
+ **Rotation strategy:** [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** [Amazon RDS Microsoft SQLServer secret structure](reference_secret_json_structure.md#reference_secret_json_structure_RDS_sqlserver)\.
+ **Source code: **[https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRDSSQLServerRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRDSSQLServerRotationMultiUser/lambda_function.py)
+ **Dependency: **Pymssql 2\.2\.2

### Amazon DocumentDB \(with MongoDB compatibility\) databases<a name="NON-RDS_rotation_templates"></a>

#### Amazon DocumentDB single user<a name="sar-template-mongodb-singleuser"></a>
+ **Template name:** SecretsManagerMongoDBRotationSingleUser
+ **Supported database/service:** Amazon DocumentDB
+ **Rotation strategy:** [Rotation strategy: single user](getting-started.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** [Amazon DocumentDB secret structure](reference_secret_json_structure.md#reference_secret_json_structure_docdb)\.
+ **Source code:** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerMongoDBRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerMongoDBRotationSingleUser/lambda_function.py)
+ **Dependency: **Pymongo 3\.2

#### Amazon DocumentDB alternating users<a name="sar-template-mongodb-multiuser"></a>
+ **Template name:** SecretsManagerMongoDBRotationMultiUser
+ **Supported database/service:** Amazon DocumentDB
+ **Rotation strategy:** [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** [Amazon DocumentDB secret structure](reference_secret_json_structure.md#reference_secret_json_structure_docdb)\.
+ **Source code: ** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerMongoDBRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerMongoDBRotationMultiUser/lambda_function.py)
+ **Dependency: **Pymongo 3\.2

### Amazon Redshift<a name="template-redshift"></a>

#### Amazon Redshift single user<a name="sar-template-redshift-singleuser"></a>
+ **Template name:** SecretsManagerRedshiftRotationSingleUser
+ **Supported database/service:** Amazon Redshift
+ **Rotation strategy:** [Rotation strategy: single user](getting-started.md#rotating-secrets-one-user-one-password)\.
+ **Expected `SecretString` structure:** [Amazon Redshift secret structure](reference_secret_json_structure.md#reference_secret_json_structure_RS)\.
+ **Source code: ** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRedshiftRotationSingleUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRedshiftRotationSingleUser/lambda_function.py)
+ **Dependency: **PyGreSQL 5\.0\.7

#### Amazon Redshift alternating users<a name="sar-template-redshift-multiuser"></a>
+ **Template name:** SecretsManagerRedshiftRotationMultiUser
+ **Supported database/service:** Amazon Redshift
+ **Rotation strategy:** [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users)\.
+ **Expected `SecretString` structure:** [Amazon Redshift secret structure](reference_secret_json_structure.md#reference_secret_json_structure_RS)\.
+ **Source code: ** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRedshiftRotationMultiUser/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRedshiftRotationMultiUser/lambda_function.py)
+ **Dependency: **PyGreSQL 5\.0\.7

### Other types of secrets<a name="OTHER_rotation_templates"></a>

Secrets Manager provides this template as a starting point for you to create a rotation function for any type of secret\. For more information, see [How rotation works](rotating-secrets.md#rotate-secrets_how)\.
+ **Template name:** SecretsManagerRotationTemplate
+ **Source code: ** [https://github\.com/aws\-samples/aws\-secrets\-manager\-rotation\-lambdas/tree/master/SecretsManagerRotationTemplate/lambda\_function\.py](https://github.com/aws-samples/aws-secrets-manager-rotation-lambdas/tree/master/SecretsManagerRotationTemplate/lambda_function.py)