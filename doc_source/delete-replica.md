# Deleting a replica secret<a name="delete-replica"></a>

You can delete each replica secret from a Region by removing the replica secret from the primary secret, and then deleting it\.

**Note**  
You cannot delete a primary secret if a replica secret exists for it\. 

To delete a replica secret, use the following steps:

1. Log into the Secrets Manager at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\. 

1. Choose the primary secret of the replica secret to display the primary secret details\.

1. In the **Replicate Secret** section, choose the replica secret\.

1. From the **Actions** menu, choose **Delete Replica**\.