# How Amazon RDS uses AWS Secrets Manager<a name="integrating_how-services-use-secrets_RDS"></a>

Amazon Relational Database Service \(Amazon RDS\) is a web service that makes it easier to set up, operate, and scale a relational database in the AWS Cloud\. To manage user credentials for Amazon RDS, we recommend that you use Secrets Manager secrets\. For more information, see [Create a database secret](create_database_secret.md) and [Security best practices for Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_BestPractices.Security.html)\.

When you call the Amazon RDS Data API, you can pass credentials for the database by using a secret in Secrets Manager\. For more information, see [Using the Data API for Aurora Serverless](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/data-api.html)\.

When you use the Amazon RDS query editor to connect to a database, it stores your credentials in a Secrets Manager secret\. For more information, see [Using the query editor for Aurora Serverless](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/query-editor.html)\.