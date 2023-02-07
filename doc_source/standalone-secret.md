# Promote a replica secret to a standalone secret in AWS Secrets Manager<a name="standalone-secret"></a>

A replica secret is a secret that is replicated from a primary in another AWS Region\. It has the same secret value and metadata as the primary, but it can be encrypted with a different KMS key\. A replica secret can't be updated independently from its primary secret, except for its encryption key\. Promoting a replica secret disconnects the replica secret from the primary secret and makes the replica secret a standalone secret\. Changes to the primary secret won't replicate to the standalone secret\. 

You might want to promote a replica secret to a standalone secret as a disaster recovery solution if the primary secret becomes unavailable\. Or you might want to promote a replica to a standalone secret if you want to turn on rotation for the replica\.

If you promote a replica, be sure to update the corresponding applications to use the standalone secret\. 

**To promote a replica secret \(console\)**

1. Log in to the Secrets Manager at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\. 

1. Navigate to the replica region\. 

1. On the **Secrets** page, choose the replica secret\.

1. On the replica secret details page, choose **Promote to standalone secret**\.

1. In the **Promote replica to standalone secret** dialog box, enter the Region and then choose **Promote replica**\.

## AWS CLI<a name="standalone-secret-cli"></a>

**Example Promote a replica secret to a primary**  
The following [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/stop-replication-to-replica.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/stop-replication-to-replica.html) example removes the link between a replica secret to the primary\. The replica secret is promoted to a primary secret in the replica region\. You must call [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/stop-replication-to-replica.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/stop-replication-to-replica.html) from within the replica region\.  

```
aws secretsmanager stop-replication-to-replica \
    --secret-id MyTestSecret
```

## AWS SDK<a name="standalone-secret-sdk"></a>

To promote a replica to a standalone secret, use the [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_StopReplicationToReplica.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_StopReplicationToReplica.html) command\. You must call this command from the replica secret Region\. For more information, see [AWS SDKs](asm_access.md#asm-sdks)\.