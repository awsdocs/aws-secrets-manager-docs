# Rotate AWS Secrets Manager secrets<a name="rotating-secrets"></a>

*Rotation* is the process of periodically updating a secret\. When you rotate a secret, you update the credentials in both the secret and the database or service\. In Secrets Manager, you can set up automatic rotation for your secrets\. Applications that [retrieve the secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/retrieving-secrets.html#retrieving-secrets_pro) from Secrets Manager automatically get the new credentials after rotation\.

To turn on automatic rotation, you need administrator permissions\. See [Secrets Manager administrator permissions](auth-and-access.md#auth-and-access_admin)\.

**Topics**
+ [Rotation strategies](rotating-secrets_strategies.md)
+ [Automatically rotate an Amazon RDS, Amazon DocumentDB, or Amazon Redshift secret](rotate-secrets_turn-on-for-db.md)
+ [Automatically rotate a secret](rotate-secrets_turn-on-for-other.md)
+ [Schedule expressions in Secrets Manager rotation](rotate-secrets_schedule.md)
+ [Rotate a secret immediately](rotate-secrets_now.md)
+ [How rotation works](rotate-secrets_how.md)
+ [Network access for the rotation function](rotation-network-rqmts.md)
+ [Permissions for rotation](rotating-secrets-required-permissions-function.md)
+ [Customize a Lambda rotation function for Secrets Manager](rotate-secrets_customize.md)
+ [Secrets Manager rotation function templates](reference_available-rotation-templates.md)
+ [Troubleshoot AWS Secrets Manager rotation of secrets](troubleshoot_rotation.md)