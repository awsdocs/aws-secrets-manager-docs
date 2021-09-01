# Restoring a secret<a name="manage_restore-secret"></a><a name="proc-restore-secret"></a>

Secrets Manager considers a secret scheduled for deletion *deprecated* and you can no longer directly access it\. After the recovery window has passed, Secrets Manager deletes the secret permanently\. Once Secrets Manager deletes the secret, you can't recover it\. Before the end of the recovery window, you can recover the secret and make it accessible again\. This removes the `DeletionDate` field, which cancels the scheduled permanent deletion\.

To restore a secret and the metadata in the console, you must have `secretsmanager:ListSecrets` and `secretsmanager:RestoreSecret` permissions\.<a name="proc-restore-secret-console"></a>

**To restore a secret \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the list of secrets, choose the secret you want to restore\. 

   If deleted secrets don't appear in your list of secrets, choose **Preferences** \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/preferences-gear.png)\)\. In the Preferences dialog box, select **Show disabled secrets**, and then choose **Save**

1. On the **Secret details** page, choose **Cancel deletion**\.

1. In the **Cancel secret deletion** dialog box, choose **Cancel deletion**\.<a name="proc-restore-secret-api"></a>

## AWS CLI<a name="manage_restore-secret_CLI"></a>

You can use the [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/restore-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/restore-secret.html) command to retrieve a secret stored in Secrets Manager\.

**Example**  
The following example restores a previously deleted secret named "MyTestDatabase"\. This cancels the scheduled deletion and restores access to the secret\.  

```
$ aws secretsmanager restore-secret --secret-id development/MyTestDatabase
{
    "ARN": "arn:aws:secretsmanager:region:accountid:secret:development/MyTestDatabase-AbCdEf",
    "Name": "development/MyTestDatabase"
}
```

## AWS SDK<a name="manage_restore-secret_SDK"></a>

To restore a secret marked for deletion, use the [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RestoreSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RestoreSecret.html) command\. For more information, see:
+ [ C\+\+](http://sdk.amazonaws.com/cpp/api/LATEST/namespace_aws_1_1_secrets_manager.html)
+ [Java](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/package-summary.html)
+ [PHP](https://docs.aws.amazon.com//aws-sdk-php/v3/api/namespace-Aws.SecretsManager.html)
+ [Python](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html)
+ [Ruby](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/SecretsManager.html)
+ [Node\.js](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/SecretsManager.html)