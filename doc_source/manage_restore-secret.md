# Restore an AWS Secrets Manager secret<a name="manage_restore-secret"></a>

Secrets Manager considers a secret scheduled for deletion *deprecated* and you can no longer directly access it\. After the recovery window has passed, Secrets Manager deletes the secret permanently\. Once Secrets Manager deletes the secret, you can't recover it\. Before the end of the recovery window, you can recover the secret and make it accessible again\. This removes the `DeletionDate` field, which cancels the scheduled permanent deletion\.

To restore a secret and the metadata in the console, you must have `secretsmanager:ListSecrets` and `secretsmanager:RestoreSecret` permissions\.

**To restore a secret \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the list of secrets, choose the secret you want to restore\. 

   If deleted secrets don't appear in your list of secrets, choose **Preferences** \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/preferences-gear.png)\)\. In the Preferences dialog box, select **Show disabled secrets**, and then choose **Save**

1. On the **Secret details** page, choose **Cancel deletion**\.

1. In the **Cancel secret deletion** dialog box, choose **Cancel deletion**\.

## AWS CLI<a name="manage_restore-secret_CLI"></a>

**Example Restore a previously deleted secret**  
The following [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/restore-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/restore-secret.html) example restores a secret that was previously scheduled for deletion\.  

```
aws secretsmanager restore-secret \
    --secret-id MyTestSecret
```

## AWS SDK<a name="manage_restore-secret_SDK"></a>

To restore a secret marked for deletion, use the [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RestoreSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RestoreSecret.html) command\. For more information, see [AWS SDKs](asm_access.md#asm-sdks)\.