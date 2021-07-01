# Retrying secret replication<a name="retry-replica"></a>

Secret replication can fail for the following reasons:
+ A secret with the same name already exists in a Region\.
+ You do not have sufficient permissions on the encryption key\.
+ You have not enabled the Region where you want a replicated secret\.
+ Other failures as described in the displayed banner\.

Before retrying the failed replication, resolve the failure reason\.

To retry replication, use the following steps:

1. Choose the secret you want to retry replication\.

1. In the **Replicate Secret** section, choose the Region with the **Replication Status** Failed\.

1. From the **Actions** menu, choose **Retry Replication**\.

If the replication fails due to a name conflict in the replica secret Region, you can choose to overwrite the existing secret in the replica Region\.

Choose the checkbox to overwrite the secret, and enter the AWS Region\.

**Replica secret information**  
After creating a primary secret with a replica secret, you can view the following replica secret information in the Secrets Manager console: 
+ **Region** \- Displays the Region of the replica secret\.
+ **Region Replication Status** \- Displays the replication status\.
  + **In Sync** \- Secret replication successful in the target Region\.
  + **In Progress** \- Secret replication in progress\.
  + **Failed** \- Secret with the same name exists in the selected Region\.
  + **Failed** \- No permissions available on the KMS key to complete the replication\.
  + **Failed ** \- Secret replication failed due to a network error\.
  + **Failed** \- You have not enabled the region where the replication occurs\.
+ **Secret ARN** \- Displays the replica Amazon Resource Number \(ARN\)\.
+ **Encryption Key** \- Displays the type of encryption key used to encrypt the replica secret\.
+ **Last Accessed Date** \- Displays the date you last accessed the replica secret\.