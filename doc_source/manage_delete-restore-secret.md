# Deleting and Restoring a Secret<a name="manage_delete-restore-secret"></a>

Because of the critical nature of secrets, AWS Secrets Manager intentionally makes deleting a secret difficult\. Secrets Manager does not immediately delete secrets\. Instead, Secrets Manager immediately makes the secrets inaccessible and scheduled for deletion after a recovery window of a *minimum* of seven days\. Until the recovery window ends, you can recover a secret you previously deleted\. By using the [CLI](#proc-deleted-secret-cli), you can delete a secret without a recovery window\.

You also can't *directly* delete a version of a secret\. Instead, you remove all staging labels from the secret\. This marks the secret as deprecated, and enables Secrets Manager to automatically delete the version in the background\.

This section includes procedures and commands describing deleting a and restoring a deleted secret:
+ [Delete a secret and all of the versions ](#proc-delete-secret)
+ [Restore a secret scheduled for deletion](#proc-restore-secret)
+ [Delete a version of a secret](#proc-delete-version)<a name="proc-delete-secret"></a>

**Deleting a secret and all of the versions**  
Follow the steps under one of the following tabs:

------
#### [ Using the Secrets Manager console ]<a name="proc-delete-secret-console"></a>

When you delete a secret, Secrets Manager immediately deprecates it\. However, Secrets Manager doesn't actually delete the secret until the number of days specified in the recovery window has elapsed\. You can't access a deprecated secret\. If you need to access a secret scheduled for deletion, you must restore the secret\. Then you can access the secret and the encrypted secret information\.

You can permanently delete a secret without any recovery window, by using the AWS CLI or AWS SDKs\. However, you can't do this in the Secrets Manager console\.

**Minimum permissions**  
To delete a secret in the console, you must have these permissions:  
`secretsmanager:ListSecrets` – Used to navigate to the secret you want to delete\.
`secretsmanager:DeleteSecret` – Used to deprecate the secret and schedule it for permanent deletion\.

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Navigate to the list of secrets you currently manage in Secrets Manager, and choose the name of the secret you to delete\.

1. In the **Secret details** section, choose **Delete secret**\.

1. In the **Schedule secret deletion** dialog box, specify the number of days in the recovery window\. This represents the number of days to wait before the deletion becomes permanent\. Secrets Manager attaches a field called `DeletionDate` and sets the field to the current date and time, plus the number of days specified for the recovery window\.

1. Choose **Schedule deletion**\.

1. If you enabled the option to show deleted items in the console, then the secret continues to display\. You can optionally choose to view the **Deleted date** field in the list\.

   1. Choose the **Preferences** icon \(the gear ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/preferences-gear.png)\) in the upper\-right corner\.

   1. Choose **Show secrets scheduled for deletion**\.

   1. Enable the switch for **Deleted on**\.

   1. Choose **Save**\.

   Your deleted secrets now appear in the list, with the date you deleted each one\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-delete-secret-api"></a><a name="proc-deleted-secret-cli"></a>

You can use the following commands to retrieve a secret stored in AWS Secrets Manager:
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DeleteSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DeleteSecret.html)
+ **AWS CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/delete-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/delete-secret.html)

You have to identify the secret to delete by the friendly name or Amazon Resource Name \(ARN\) in the `SecretId` field\.

**Example**  
The following example of the AWS CLI command deprecates the secret named "MyTestDatabase" and schedules the secret for deletion after a recovery window of 14 days\.  

```
$ aws secretsmanager delete-secret --secret-id development/MyTestDatabase --recovery-window-in-days 14
{
    "ARN": "arn:aws:secretsmanager:region:accountid:secret:development/MyTestDatabase-AbCdEf",
    "Name": "development/MyTestDatabase",
    "DeletionDate": 1510089380.309
}
```
At any time after the date and time specified in the `DeletionDate` field, AWS Secrets Manager permanently deletes the secret\.

You can also delete a secret immediately with no waiting time\.

**Important**  
A secret deleted with the `ForceDeleteWithoutRecovery` parameter cannot be recovered with the `RestoreSecret` operation\.

**Example**  
The following example of the AWS CLI command immediately deletes the secret without a recovery window\. The `DeletionDate` response field shows the current date and time instead of a future time\.  

```
$ aws secretsmanager delete-secret --secret-id development/MyTestDatabase --force-delete-without-recovery
{
    "ARN": "arn:aws:secretsmanager:region:accountid:secret:development/MyTestDatabase-AbCdEf",
    "Name": "development/MyTestDatabase",
    "DeletionDate": 1508750180.309
}
```

**Handy tip**  
If you don't know an application still uses a secret, you can create an Amazon CloudWatch alarm to alert you to any attempts to access a secret during the recovery window\. For more information, see [Monitoring Secret Versions Scheduled for Deletion](monitoring.md#monitoring_cloudwatch_deleted-secrets)\.

------<a name="proc-restore-secret"></a>

**Restoring a secret scheduled for deletion**  
Follow the steps under one of the following tabs:

------
#### [ Using the Secrets Manager console ]<a name="proc-restore-secret-console"></a>

Secrets Manager considers a secret scheduled for deletion deprecated and no longer directly accessed\. After the recovery window has passed, Secrets Manager deletes the secret permanently\. Once Secrets Manager deletes the secret, you can't recover it\. Before the end of the recovery window, you can recover the secret and make it accessible again\. This removes the `DeletionDate` field, which cancels the scheduled permanent deletion\.

**Minimum permissions**  
To restore a secret and the metadata in the console, you must have these permissions:  
`secretsmanager:ListSecrets` – Use to navigate to the secret you want to restore\.
`secretsmanager:RestoreSecret` – Use to delete any versions still associated with the secret\.

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Navigate to the list of secrets you currently manage in Secrets Manager\.

1. To view deleted secrets, you must enable this ability in the console\. If you haven't enabled this feature, perform these steps:

1. 

   1. Choose the **Preferences** icon, the gear ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/preferences-gear.png), in the upper\-right corner\.

   1. Choose **Show secrets scheduled for deletion**\.

   1. Enable the switch for **Deleted on**\.

   1. Choose **Save**\.

   Your deleted secrets now appear in the list with the date you deleted each one\.

1. Choose the name of the deleted secret to restore\.

1. In the **Secret details** section, choose **Cancel deletion**\.

1. In the **Cancel secret deletion** confirmation dialog box, choose **Cancel deletion**\.

   AWS Secrets Manager removes the `DeletionDate` field from the secret\. This cancels the scheduled deletion and restores access to the secret\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-restore-secret-api"></a>

You can use the following commands to retrieve a secret stored in AWS Secrets Manager:
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RestoreSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RestoreSecret.html)
+ **AWS CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/restore-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/restore-secret.html)

You must identify the secret you want to restore by the friendly name or ARN in the `SecretId` field\.

**Example**  
The following example of the AWS CLI command restores a previously deleted secret named "MyTestDatabase"\. This cancels the scheduled deletion and restores access to the secret\.  

```
$ aws secretsmanager restore-secret --secret-id development/MyTestDatabase
{
    "ARN": "arn:aws:secretsmanager:region:accountid:secret:development/MyTestDatabase-AbCdEf",
    "Name": "development/MyTestDatabase"
}
```

------<a name="proc-delete-version"></a>

**Deleting one version of a secret**  
Follow the steps under one of the following tabs:

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-delete-version-api"></a>

You can't delete one version of a secret using the Secrets Manager console\. You must use the AWS CLI or AWS API\.

You can't directly delete a version of a secret\. Instead, you remove all of the staging labels, which effectively marks the secret as deprecated\. Secrets Manager can then delete it in the background\.

You can use the following commands to deprecate a version of a secret stored in AWS Secrets Manager:
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecretVersionStage.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UpdateSecretVersionStage.html)
+ **AWS CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret-version-stage.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/update-secret-version-stage.html)

You must identify the secret by the friendly name or ARN\. You also specify the staging labels you want to add, move, or remove\.

You can specify `FromSecretVersionId` and `MoveToSecretId` in the following combinations:
+ `FromSecretVersionId` only: This deletes staging labels completely from the specified version\. 
+ `MoveToVersionId` only: This adds the staging labels to the specified version\. If other versions have any of the staging labels already attached, Secrets Manager automatically removes the labels from those versions\.
+ `MoveToVersionId` and `RemoveFromVersionId`: These explicitly move a label\. The staging label must be present on the `RemoveFromVersionId` version of the secret, or an error occurs\.

**Example**  
The following example of the AWS CLI command removes the `AWSPREVIOUS` staging label from a version of the secret named "MyTestDatabase"\. You can retrieve the version ID of the version you want to delete by using the [ListSecretVersionIds](AWS Secrets Manager API ReferenceAPI_ListSecretVersionIds.html) command\.  

```
$ aws secretsmanager update-secret-version-stage \
        --secret-id development/MyTestDatabase \
        --remove-from-version-id EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE 
        --version-stage AWSPREVIOUS
{
    "ARN": "arn:aws:secretsmanager:region:accountid:secret:development/MyTestDatabase-AbCdEf",
    "Name": "development/MyTestDatabase"
}
```

------