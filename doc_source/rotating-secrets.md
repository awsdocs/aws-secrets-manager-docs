# Rotate AWS Secrets Manager secrets<a name="rotating-secrets"></a>

*Rotation* is the process of periodically updating a secret\. When you rotate a secret, you update the credentials in both the secret and the database or service\. In Secrets Manager, you can set up automatic rotation for your secrets\. 

**Topics**
+ [How rotation works](#rotate-secrets_how)
+ [Managed rotation](rotate-secrets_managed.md)
+ [Automatic rotation for database secrets \(console\)](rotate-secrets_turn-on-for-db.md)
+ [Automatic rotation \(console\)](rotate-secrets_turn-on-for-other.md)
+ [Automatic rotation \(AWS CLI\)](rotate-secrets-cli.md)
+ [Rotate a secret immediately](rotate-secrets_now.md)
+ [Troubleshoot rotation](troubleshoot_rotation.md)

## How rotation works<a name="rotate-secrets_how"></a>

**Tip**  
For some [Secrets managed by other services](service-linked-secrets.md), you use *managed rotation*\. To use [Managed rotation](rotate-secrets_managed.md), you first create the secret through the managing service\.

Secrets Manager rotation uses an AWS Lambda function to update the secret and the database\. For information about the costs of using a Lambda function, see [Pricing](intro.md#asm_pricing)\.

To rotate a secret, Secrets Manager calls a Lambda function according to the schedule you set up\. You can set a schedule to rotate after a period of time, for example every 30 days, or you can create a cron expression\. See [Schedule expressions](rotate-secrets_schedule.md)\. If you also manually update your secret value while automatic rotation is set up, then Secrets Manager considers that a valid rotation when it calculates the next rotation date\. 

Secrets Manager uses [staging labels](https://docs.aws.amazon.com/secretsmanager/latest/userguide/getting-started.html#term_version) to label secret versions during rotation\. During rotation, Secrets Manager calls the same function several times, each time with different parameters\. Secrets Manager invokes the function with the following JSON request structure of parameters: 

```
{
    "Step" : "request.type",
    "SecretId" : "string",
    "ClientRequestToken" : "string"
}
```

The rotation function does the work of rotating the secret\. There are four steps to rotating a secret, which correspond to the following four steps in the Lambda rotation function:

1. **Create a new version of the secret \(`createSecret`\)**

   The first step of rotation is to create a new version of the secret\. The new version can contain a new password, a new username and password, or more secret information\. Secrets Manager labels the new version with the staging label `AWSPENDING`\.

1. **Change the credentials in the database or service \(`setSecret`\)**

   Next, rotation changes the credentials in the database or service to match the new credentials in the `AWSPENDING` version of the secret\. Depending on your rotation strategy, this step can create a new user with the same permissions as the existing user\. 

   Rotation functions for Amazon RDS \(except Oracle\) and Amazon DocumentDB automatically use Secure Socket Layer \(SSL\) or Transport Layer Security \(TLS\) to connect to your database, if it is available\. Otherwise they use an unencrypted connection\.
**Note**  
If you set up automatic secret rotation before December 20, 2021, your rotation function might be based on an older template that did not support SSL/TLS\. See [Determine when your rotation function was created](troubleshoot_rotation.md#rotation-function-created-date)\. If it was created before December 20, 2021, to support connections that use SSL/TLS, you need to [recreate your rotation function](rotate-secrets_turn-on-for-db.md)\.

1. **Test the new secret version \(`testSecret`\)**

   Next, rotation tests the `AWSPENDING` version of the secret by using it to access the database or service\. Rotation functions based on [Rotation function templates](reference_available-rotation-templates.md) test the new secret by using read access\. Depending on the type of access your applications need, you can update the function to include other access such as write access\.

1. **Finish the rotation \(`finishSecret`\)**

   Finally, rotation moves the label `AWSCURRENT` from the previous secret version to this version\. Secrets Manager adds the `AWSPREVIOUS` staging label to the previous version, so that you retain the last known good version of the secret\. 

During rotation, Secrets Manager logs events that indicate the state of rotation\. For more information, see [Logging AWS Secrets Manager events with AWS CloudTrail](retrieve-ct-entries.md)\.

If any rotation step fails, Secrets Manager retries the entire rotation process multiple times\.

When rotation is successful, the `AWSPENDING` staging label might be attached to the same version as the `AWSCURRENT` version, or it might not be attached to any version\. If the `AWSPENDING` staging label is present but not attached to the same version as `AWSCURRENT`, then any later invocation of rotation assumes that a previous rotation request is still in progress and returns an error\. When rotation is unsuccessful, the `AWSPENDING` staging label might be attached to an empty secret version\. For more information, see [Troubleshoot rotation](troubleshoot_rotation.md)\.

After rotation is successful, applications that [Retrieve secrets from AWS Secrets Manager](retrieving-secrets.md) from Secrets Manager automatically get the updated credentials\. For more details about how each step of rotation works, see the [AWS Secrets Manager rotation function templates](reference_available-rotation-templates.md)\.