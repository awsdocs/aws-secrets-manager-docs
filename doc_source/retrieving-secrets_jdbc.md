# Connect to a SQL database with credentials in an AWS Secrets Manager secret<a name="retrieving-secrets_jdbc"></a>

In Java applications, you can use the Secrets Manager SQL Connection drivers to connect to MySQL, PostgreSQL, Oracle, and MSSQLServer databases using credentials stored in Secrets Manager\. Each driver wraps the base JDBC driver, so you can use JDBC calls to access your database\. However, instead of passing a username and password for the connection, you provide the ID of a secret\. The driver calls Secrets Manager to retrieve the secret value, and then uses the credentials and connection information in the secret to connect to the database\. The driver also caches the credentials using the [Java client\-side caching library](retrieving-secrets_cache-java.md), so future connections don't require a call to Secrets Manager\. The cache refreshes every hour and also when the secret is rotated\.

You can download the source code from [GitHub](https://github.com/aws/aws-secretsmanager-jdbc )\.

To use the Secrets Manager SQL Connection drivers:
+ Your application must be in Java 8 or higher\.
+ Your secret must be in the following format:

  ```
  {
    "username": "username",
    "password": "EXAMPLE-PASSWORD",
    "engine": "engineType",
    "host": "host",
    "port": portNumber,
    "dbInstanceIdentifier": "databaseId"
  }
  ```

  To check the format of your secret, in the Secrets Manager console, view your secret and choose **Retrieve secret value**\. Alternatively, in the AWS CLI, call [get\-secret\-value](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html)\.

To add the driver to your project, in your Maven build file `pom.xml`, add the following dependency for the driver\. For more information, see [Secrets Manager SQL Connection Library](https://mvnrepository.com/artifact/com.amazonaws.secretsmanager/aws-secretsmanager-jdbc) on the Maven Repository website\.

```
<dependency>
    <groupId>com.amazonaws.secretsmanager</groupId>
    <artifactId>aws-secretsmanager-jdbc</artifactId>
    <version>1.0.5</version>
</dependency>
```

**Example Establish a connection to a database**  
The following example shows how to establish a connection to a database using the credentials and connection information in a secret\. Once you have the connection, you can use JDBC calls to access the database\. For more information, see [JDBC Basics](https://docs.oracle.com/javase/tutorial/jdbc/basics/index.html) on the Java documentation website\.  

```
// Load the JDBC driver
Class.forName( "com.amazonaws.secretsmanager.sql.AWSSecretsManagerMySQLDriver" ).newInstance();

// Either retrieve the connection info from the secret or hardcode the endpoint URL
// String URL = "jdbc-secretsmanager:mysql://example.com:3306";
String URL = "secretId";

// Populate the user property with the secret ARN to retrieve user and password from the secret
Properties info = new Properties( );
info.put( "user", "secretId" );

// Establish the connection
conn = DriverManager.getConnection(URL, info);
```

```
// Load the JDBC driver
Class.forName( "com.amazonaws.secretsmanager.sql.AWSSecretsManagerPostgreSQLDriver" ).newInstance();

// Either retrieve the connection info from the secret or hardcode the endpoint URL
// String URL = "jdbc-secretsmanager:postgresql://example.com:5432/database";
String URL = "secretId";

// Populate the user property with the secret ARN to retrieve user and password from the secret
Properties info = new Properties( );
info.put( "user", "secretId" );

// Establish the connection
conn = DriverManager.getConnection(URL, info);
```

```
// Load the JDBC driver
Class.forName( "com.amazonaws.secretsmanager.sql.AWSSecretsManagerOracleDriver" ).newInstance();

// Either retrieve the connection info from the secret or hardcode the endpoint URL
// String URL = "jdbc-secretsmanager:oracle:thin:@example.com:1521/ORCL";
String URL = "secretId";

// Populate the user property with the secret ARN to retrieve user and password from the secret
Properties info = new Properties( );
info.put( "user", "secretId" );

// Establish the connection
conn = DriverManager.getConnection(URL, info);
```

```
// Load the JDBC driver
Class.forName( "com.amazonaws.secretsmanager.sql.AWSSecretsManagerMSSQLServerDriver" ).newInstance();

// Either retrieve the connection info from the secret or hardcode the endpoint URL
// String URL = "jdbc-secretsmanager:sqlserver://example.com:1433";
String URL = "secretId";

// Populate the user property with the secret ARN to retrieve user and password from the secret
Properties info = new Properties( );
info.put( "user", "secretId" );

// Establish the connection
conn = DriverManager.getConnection(URL, info);
```

**Example Use c3p0 connection pooling to establish a connection**  
The following example shows a `c3p0.properties` file that uses the driver to retrieve credentials and the endpoint from the secret\. For `user` and `jdbcUrl`, enter the secret ID to configure the connection pool\. Then you can retrieve connections from the pool and use them as any other database connections\. For more information, see [JDBC Basics](https://docs.oracle.com/javase/tutorial/jdbc/basics/index.html) on the Java documentation website\.  
For more information about c3p0, see [c3p0](https://www.mchange.com/projects/c3p0/) on the Machinery For Change website\.   

```
c3p0.user=secretId
c3p0.driverClass=com.amazonaws.secretsmanager.sql.AWSSecretsManagerMySQLDriver
c3p0.jdbcUrl=secretId
```

```
c3p0.user=secretId
c3p0.driverClass=com.amazonaws.secretsmanager.sql.AWSSecretsManagerPostgreSQLDriver
c3p0.jdbcUrl=secretId
```

```
c3p0.user=secretId
c3p0.driverClass=com.amazonaws.secretsmanager.sql.AWSSecretsManagerOracleDriver
c3p0.jdbcUrl=secretId
```

```
c3p0.user=secretId
c3p0.driverClass=com.amazonaws.secretsmanager.sql.AWSSecretsManagerMSSQLServerDriver
c3p0.jdbcUrl=secretId
```
The following example shows how to connect to a different endpoint than the one in the secret by changing `jdbcUrl` to your endpoint\. Then you can retrieve connections from the pool and use them as any other database connections\. For more information, see [JDBC Basics](https://docs.oracle.com/javase/tutorial/jdbc/basics/index.html) on the Java documentation website\.  

```
c3p0.user=secretId
c3p0.driverClass=com.amazonaws.secretsmanager.sql.AWSSecretsManagerMySQLDriver
c3p0.jdbcUrl=jdbc-secretsmanager:mysql://example.com:3306
```

```
c3p0.user=secretId
c3p0.driverClass=com.amazonaws.secretsmanager.sql.AWSSecretsManagerPostgreSQLDriver
c3p0.jdbcUrl=jdbc-secretsmanager:postgresql://example.com:5432/database
```

```
c3p0.user=secretId
c3p0.driverClass=com.amazonaws.secretsmanager.sql.AWSSecretsManagerOracleDriver
c3p0.jdbcUrl=jdbc-secretsmanager:oracle:thin:@example.com:1521/ORCL
```

```
c3p0.user=secretId
c3p0.driverClass=com.amazonaws.secretsmanager.sql.AWSSecretsManagerMSSQLServerDriver
c3p0.jdbcUrl=jdbc-secretsmanager:sqlserver://example.com:1433
```