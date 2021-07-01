# AWS Secrets Manager Best practices<a name="best-practices"></a>

The following recommendations help you to more securely use AWS Secrets Manager:

**Topics**
+ [Protecting additional sensitive information](#best-practice_what-not-to-put-in-secret-text)
+ [Improving performance by using the AWS provided client\-side caching components](best-practice_client-caching-components.md)
+ [Adding retries to your application](throttling.md)
+ [Mitigating the risks of logging and debugging your Lambda function](best-practice_lamda-debug-statements.md)
+ [Mitigating the risks of using the AWS CLI to store your secrets](best-practice_cli-exposure-risks.md)
+ [Cross\-account access – should I specify a user/role or the account?](best-practice_cross-account-role-vs-account.md)
+ [Running everything in a VPC](best-practice_using-a-vpc.md)
+ [Tag your secrets](best-practice_tagging.md)
+ [Rotating secrets on a schedule](best-practice-rotating-secrets.md)
+ [Auditing access to secrets](audit-secrets.md)
+ [Monitoring your secrets with AWS Config](aws-config.md)
+ [Using Secrets Manager to provide database credentials to Lambda functions](lambda-functions.md)
+ [More resources on best practices](more-resources.md)

## Protecting additional sensitive information<a name="best-practice_what-not-to-put-in-secret-text"></a>

A secret often includes several pieces of information besides the user name and password\. Depending on the database, service, or website, you can choose to include additional sensitive data\. This data can include password hints, or question\-and\-answer pairs you can use to recover your password\.

Ensure you protect any information that might be used to gain access to the credentials in the secret as securely as the credentials themselves\. Don't store this type of information in the `Description` or any other non\-encrypted part of the secret\.

Instead, store all such sensitive information as part of the encrypted secret value, either in the `SecretString` or `SecretBinary` field\. You can store up to 65536 bytes in the secret\. In the `SecretString` field, the text usually takes the form of JSON key\-value string pairs, as shown in the following example:

```
{
  "engine": "mysql",
  "username": "user1",
  "password": "i29wwX!%9wFV",
  "host": "my-database-endpoint.us-east-1.rds.amazonaws.com",
  "dbname": "myDatabase",
  "port": "3306"
}
```