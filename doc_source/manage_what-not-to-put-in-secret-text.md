# Protect additional sensitive information<a name="manage_what-not-to-put-in-secret-text"></a>

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