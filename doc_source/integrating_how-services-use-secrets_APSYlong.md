# How AWS AppSync uses AWS Secrets Manager<a name="integrating_how-services-use-secrets_APSYlong"></a>

AWS AppSync provides a robust, scalable GraphQL interface for application developers to combine data from multiple sources, including Amazon DynamoDB, AWS Lambda, and HTTP APIs\.

AWS AppSync uses the CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds-data/execute-statement.html](https://docs.aws.amazon.com/cli/latest/reference/rds-data/execute-statement.html) to connect to Amazon RDS using the credentials in a secret\. For more information, see [Tutorial: Aurora Serverless](https://docs.aws.amazon.com/appsync/latest/devguide/tutorial-rds-resolvers.html)\.