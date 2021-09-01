# Deleting a secret<a name="manage_delete-secret"></a><a name="proc-delete-version"></a>

Because of the critical nature of secrets, AWS Secrets Manager intentionally makes deleting a secret difficult\. Secrets Manager does not immediately delete secrets\. Instead, Secrets Manager immediately makes the secrets inaccessible and scheduled for deletion after a recovery window of a minimum of seven days\. Until the recovery window ends, you can recover a secret you previously deleted\. There is no charge for secrets that you have marked for deletion\. 

You also can't directly delete a version of a secret\. Instead, you remove all staging labels from the secret using the AWS CLI or AWS SDK\. This marks the secret as deprecated, and then Secrets Manager can automatically delete the version in the background\.

If you don't know whether an application still uses a secret, you can create an Amazon CloudWatch alarm to alert you to any attempts to access a secret during the recovery window\. For more information, see [Monitoring secret versions scheduled for deletion](monitoring.md#monitoring_cloudwatch_deleted-secrets)\.

To delete a secret, you must have `secretsmanager:ListSecrets` and `secretsmanager:DeleteSecret` permissions\.<a name="proc-delete-secret"></a><a name="proc-delete-secret-console"></a>

**To delete a secret \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the list of secrets, choose the secret you want to delete\.

1. In the **Secret details** section, choose **Actions**, and then choose **Delete secret**\.

1. In the **Disable secret and schedule deletion** dialog box, in **Waiting period**, enter the number of days to wait before the deletion becomes permanent\. Secrets Manager attaches a field called `DeletionDate` and sets the field to the current date and time, plus the number of days specified for the recovery window\.

1. Choose **Schedule deletion**\.

**To view deleted secrets**

1. On the **Secrets** page, choose **Preferences** \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/preferences-gear.png)\)\. 

1. In the Preferences dialog box, select **Show disabled secrets**, and then choose **Save**<a name="proc-deleted-secret-cli"></a>

## AWS CLI<a name="manage_delete-secret_cli"></a>

To delete a secret by using the AWS CLI, use the [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/delete-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/delete-secret.html) action\. To delete a version of a secret, use the [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret-version-stage.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret-version-stage.html) action to remove all of the staging labels\. Secrets Manager can then delete the version in the background\. To find the version ID of the version you want to delete, use [ListSecretVersionIds](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ListSecretVersionIds.html)\.

**Example**  
The following example marks for deletion the secret named "MyTestDatabase" and schedules deletion after a recovery window of 14 days\. At any time after the date and time specified in the `DeletionDate` field, Secrets Manager permanently deletes the secret\.  

```
$ aws secretsmanager delete-secret --secret-id development/MyTestDatabase --recovery-window-in-days 14
{
    "ARN": "arn:aws:secretsmanager:region:accountid:secret:development/MyTestDatabase-AbCdEf",
    "Name": "development/MyTestDatabase",
    "DeletionDate": 1510089380.309
}
```

**Example**  
The following example immediately deletes the secret without a recovery window\. The `DeletionDate` response field shows the current date and time instead of a future time\. **This secret cannot be recovered\.**  

```
$ aws secretsmanager delete-secret --secret-id development/MyTestDatabase --force-delete-without-recovery
{
    "ARN": "arn:aws:secretsmanager:region:accountid:secret:development/MyTestDatabase-AbCdEf",
    "Name": "development/MyTestDatabase",
    "DeletionDate": 1508750180.309
}
```

**Example**  
The following example removes the `AWSPREVIOUS` staging label from a version of the secret named "MyTestDatabase"\.   

```
$ aws secretsmanager update-secret-version-stage \
        --secret-id development/MyTestDatabase \
        --remove-from-version-id EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE 
        --version-stage AWSPREVIOUS
{
    "ARN": "arn:aws:secretsmanager:region:accountid:secret:development/MyTestDatabase-AbCdEf",
    "Name": "development/MyTestDatabase"
}
```<a name="proc-delete-secret-api"></a><a name="proc-delete-version-api"></a>

## AWS SDK;<a name="manage_delete-secret_sdk"></a>

To delete a secret, use the [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DeleteSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DeleteSecret.html) command\. To delete a version of a secret, use the [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecretVersionStage.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecretVersionStage.html) command\. For more information, see:
+ [AC\+\+](http://sdk.amazonaws.com/cpp/api/LATEST/namespace_aws_1_1_secrets_manager.html)
+ [Java](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/package-summary.html)
+ [PHP](https://docs.aws.amazon.com//aws-sdk-php/v3/api/namespace-Aws.SecretsManager.html)
+ [Python](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html)
+ [Ruby](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/SecretsManager.html)
+ [Node\.js](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/SecretsManager.html)