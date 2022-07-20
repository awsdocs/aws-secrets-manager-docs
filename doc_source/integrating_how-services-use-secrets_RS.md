# How Amazon Redshift uses AWS Secrets Manager<a name="integrating_how-services-use-secrets_RS"></a>

Amazon Redshift is a fully managed, petabyte\-scale data warehouse service in the cloud\. To manage user credentials for Amazon Redshift, we recommend you use Secrets Manager secrets\. For more information, see [Create a database secret](create_database_secret.md) and [Storing database credentials in AWS Secrets Manager](https://docs.aws.amazon.com/redshift/latest/mgmt/data-api.html#data-api-secrets)\.

When you call the Amazon Redshift Data API, you can pass credentials for the cluster by using a secret in Secrets Manager\. For more information, see [Using the Amazon Redshift Data API](https://docs.aws.amazon.com/redshift/latest/mgmt/data-api.html)\.

When you use the Amazon Redshift query editor to connect to a database, Amazon Redshift can store your credentials in a Secrets Manager secret with the prefix `redshiftqueryeditor`\. You are charged for that secret\. For more information, see [Querying a database using the query editor](https://docs.aws.amazon.com/redshift/latest/mgmt/query-editor.html) in the *Amazon Redshift Cluster Management Guide*\.

For query editor v2, see [How Amazon Redshift query editor v2 uses AWS Secrets Manager](integrating_how-services-use-secrets_sqlworkbench.md)\.