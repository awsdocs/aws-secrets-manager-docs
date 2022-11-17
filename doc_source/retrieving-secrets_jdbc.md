# Connect to a SQL database with credentials in an AWS Secrets Manager secret<a name="retrieving-secrets_jdbc"></a>

In Java applications, you can use the Secrets Manager SQL Connection drivers to connect to MySQL, PostgreSQL, Oracle, and MSSQLServer databases using credentials stored in Secrets Manager\. Each driver wraps the base JDBC driver, so you can use JDBC calls to access your database\. However, instead of passing a username and password for the connection, you provide the ID of a secret\. The driver calls Secrets Manager to retrieve the secret value, and then uses the credentials in the secret to connect to the database\. The driver also caches the credentials using the [Java client\-side caching library](retrieving-secrets_cache-java.md), so future connections don't require a call to Secrets Manager\. By default, the cache refreshes every hour and also when the secret is rotated\. To configure the cache, see [SecretCacheConfiguration](retrieving-secrets_cache-java-ref_SecretCacheConfiguration.md)\.

You can download the source code from [GitHub](https://github.com/aws/aws-secretsmanager-jdbc )\.

To use the Secrets Manager SQL Connection drivers:
+ Your application must be in Java 8 or higher\.
+ Your secret must be in the [JSON structure of a secret](reference_secret_json_structure.md)\. To check the format, in the Secrets Manager console, view your secret and choose **Retrieve secret value**\. Alternatively, in the AWS CLI, call [get\-secret\-value](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html)\.
+ If your database is replicated to other Regions, to connect to a replica database in another Region, you specify the regional endpoint and port when you create the connection\. You can store regional connection information in the secret as extra key/value pairs, in SSM Parameter Store parameters, or in your code configuration\. 

To add the driver to your project, in your Maven build file `pom.xml`, add the following dependency for the driver\. For more information, see [Secrets Manager SQL Connection Library](https://search.maven.org/artifact/com.amazonaws.secretsmanager/aws-secretsmanager-jdbc) on the Maven Central Repository website\.

```
<dependency>
    <groupId>com.amazonaws.secretsmanager</groupId>
    <artifactId>aws-secretsmanager-jdbc</artifactId>
    <version>1.0.5</version>
</dependency>
```

**Topics**
+ [Establish a connection to a database](#retrieving-secrets_jdbc_example)
+ [Establish a connection to a replica database](#retrieving-secrets_jdbc_example_replica)
+ [Use c3p0 connection pooling to establish a connection](#retrieving-secrets_jdbc_example_c3po)
+ [Use c3p0 connection pooling to establish a connection to a replica database](#retrieving-secrets_jdbc_example_c3p0_replica)

## Establish a connection to a database<a name="retrieving-secrets_jdbc_example"></a>

The following example shows how to establish a connection to a database using the credentials and connection information in a secret\. Once you have the connection, you can use JDBC calls to access the database\. For more information, see [JDBC Basics](https://docs.oracle.com/javase/tutorial/jdbc/basics/index.html) on the Java documentation website\.

------
#### [ MySQL ]

```
// Load the JDBC driver
Class.forName( "com.amazonaws.secretsmanager.sql.AWSSecretsManagerMySQLDriver" ).newInstance();

// Retrieve the connection info from the secret 
String URL = "secretId";

// Populate the user property with the secret ARN to retrieve user and password from the secret
Properties info = new Properties( );
info.put( "user", "secretId" );

// Establish the connection
conn = DriverManager.getConnection(URL, info);
```

------
#### [ PostgreSQL ]

```
// Load the JDBC driver
Class.forName( "com.amazonaws.secretsmanager.sql.AWSSecretsManagerPostgreSQLDriver" ).newInstance();

// Retrieve the connection info from the secret 
String URL = "secretId";

// Populate the user property with the secret ARN to retrieve user and password from the secret
Properties info = new Properties( );
info.put( "user", "secretId" );

// Establish the connection
conn = DriverManager.getConnection(URL, info);
```

------
#### [ Oracle ]

```
// Load the JDBC driver
Class.forName( "com.amazonaws.secretsmanager.sql.AWSSecretsManagerOracleDriver" ).newInstance();

// Retrieve the connection info from the secret 
String URL = "secretId";

// Populate the user property with the secret ARN to retrieve user and password from the secret
Properties info = new Properties( );
info.put( "user", "secretId" );

// Establish the connection
conn = DriverManager.getConnection(URL, info);
```

------
#### [ MSSQLServer ]

```
// Load the JDBC driver
Class.forName( "com.amazonaws.secretsmanager.sql.AWSSecretsManagerMSSQLServerDriver" ).newInstance();

// Retrieve the connection info from the secret 
String URL = "secretId";

// Populate the user property with the secret ARN to retrieve user and password from the secret
Properties info = new Properties( );
info.put( "user", "secretId" );

// Establish the connection
conn = DriverManager.getConnection(URL, info);
```

------

## Establish a connection to a replica database<a name="retrieving-secrets_jdbc_example_replica"></a>

The following example shows how to establish a connection to a database using the credentials in a secret\. To connect to a replica database in another Region, you specify the endpoint and port for the connection\. 

Secrets that are replicated to other Regions can improve latency for the connection to the regional database, but they do not contain different connection information from the source secret\. Each replica is a copy of the source secret\. To store regional connection information in the secret, add more key/value pairs for the endpoint and port information for the Regions\.

Once you have the connection, you can use JDBC calls to access the database\. For more information, see [JDBC Basics](https://docs.oracle.com/javase/tutorial/jdbc/basics/index.html) on the Java documentation website\.

------
#### [ MySQL ]

```
// Load the JDBC driver
Class.forName( "com.amazonaws.secretsmanager.sql.AWSSecretsManagerMySQLDriver" ).newInstance();

// Set the endpoint and port. You can also retrieve it from a key/value pair in the secret.
String URL = "jdbc-secretsmanager:mysql://example.com:3306";

// Populate the user property with the secret ARN to retrieve user and password from the secret
Properties info = new Properties( );
info.put( "user", "secretId" );

// Establish the connection
conn = DriverManager.getConnection(URL, info);
```

------
#### [ PostgreSQL ]

```
// Load the JDBC driver
Class.forName( "com.amazonaws.secretsmanager.sql.AWSSecretsManagerPostgreSQLDriver" ).newInstance();

// Set the endpoint and port. You can also retrieve it from a key/value pair in the secret.
String URL = "jdbc-secretsmanager:postgresql://example.com:5432/database";

// Populate the user property with the secret ARN to retrieve user and password from the secret
Properties info = new Properties( );
info.put( "user", "secretId" );

// Establish the connection
conn = DriverManager.getConnection(URL, info);
```

------
#### [ Oracle ]

```
// Load the JDBC driver
Class.forName( "com.amazonaws.secretsmanager.sql.AWSSecretsManagerOracleDriver" ).newInstance();

// Set the endpoint and port. You can also retrieve it from a key/value pair in the secret.
String URL = "jdbc-secretsmanager:oracle:thin:@example.com:1521/ORCL";

// Populate the user property with the secret ARN to retrieve user and password from the secret
Properties info = new Properties( );
info.put( "user", "secretId" );

// Establish the connection
conn = DriverManager.getConnection(URL, info);
```

------
#### [ MSSQLServer ]

```
// Load the JDBC driver
Class.forName( "com.amazonaws.secretsmanager.sql.AWSSecretsManagerMSSQLServerDriver" ).newInstance();

// Set the endpoint and port. You can also retrieve it from a key/value pair in the secret.
String URL = "jdbc-secretsmanager:sqlserver://example.com:1433";

// Populate the user property with the secret ARN to retrieve user and password from the secret
Properties info = new Properties( );
info.put( "user", "secretId" );

// Establish the connection
conn = DriverManager.getConnection(URL, info);
```

------

## Use c3p0 connection pooling to establish a connection<a name="retrieving-secrets_jdbc_example_c3po"></a>

The following example shows a `c3p0.properties` file that uses the driver to retrieve credentials and connection information from the secret\. For `user` and `jdbcUrl`, enter the secret ID to configure the connection pool\. Then you can retrieve connections from the pool and use them as any other database connections\. For more information, see [JDBC Basics](https://docs.oracle.com/javase/tutorial/jdbc/basics/index.html) on the Java documentation website\.

For more information about c3p0, see [c3p0](https://www.mchange.com/projects/c3p0/) on the Machinery For Change website\. 

------
#### [ MySQL ]

```
c3p0.user=secretId
c3p0.driverClass=com.amazonaws.secretsmanager.sql.AWSSecretsManagerMySQLDriver
c3p0.jdbcUrl=secretId
```

------
#### [ PostgreSQL ]

```
c3p0.user=secretId
c3p0.driverClass=com.amazonaws.secretsmanager.sql.AWSSecretsManagerPostgreSQLDriver
c3p0.jdbcUrl=secretId
```

------
#### [ Oracle ]

```
c3p0.user=secretId
c3p0.driverClass=com.amazonaws.secretsmanager.sql.AWSSecretsManagerOracleDriver
c3p0.jdbcUrl=secretId
```

------
#### [ MSSQLServer ]

```
c3p0.user=secretId
c3p0.driverClass=com.amazonaws.secretsmanager.sql.AWSSecretsManagerMSSQLServerDriver
c3p0.jdbcUrl=secretId
```

------

## Use c3p0 connection pooling to establish a connection to a replica database<a name="retrieving-secrets_jdbc_example_c3p0_replica"></a>

The following example shows a `c3p0.properties` file that uses the driver to retrieve credentials and from the secret\. To connect to a replica database in another Region, you specify the endpoint and port directly or by retrieving them from key/value pairs in the secret\. Then you can retrieve connections from the pool and use them as any other database connections\. For more information, see [JDBC Basics](https://docs.oracle.com/javase/tutorial/jdbc/basics/index.html) on the Java documentation website\.

------
#### [ MySQL ]

```
c3p0.user=secretId
c3p0.driverClass=com.amazonaws.secretsmanager.sql.AWSSecretsManagerMySQLDriver
c3p0.jdbcUrl=jdbc-secretsmanager:mysql://example.com:3306
```

------
#### [ PostgreSQL ]

```
c3p0.user=secretId
c3p0.driverClass=com.amazonaws.secretsmanager.sql.AWSSecretsManagerPostgreSQLDriver
c3p0.jdbcUrl=jdbc-secretsmanager:postgresql://example.com:5432/database
```

------
#### [ Oracle ]

```
c3p0.user=secretId
c3p0.driverClass=com.amazonaws.secretsmanager.sql.AWSSecretsManagerOracleDriver
c3p0.jdbcUrl=jdbc-secretsmanager:oracle:thin:@example.com:1521/ORCL
```

------
#### [ MSSQLServer ]

```
c3p0.user=secretId
c3p0.driverClass=com.amazonaws.secretsmanager.sql.AWSSecretsManagerMSSQLServerDriver
c3p0.jdbcUrl=jdbc-secretsmanager:sqlserver://example.com:1433
```

------