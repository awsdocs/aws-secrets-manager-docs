# Editing an encryption key<a name="edit-key"></a>

When you change the encryption key associated with your replica secret, all future secret versions use the new key\. Be sure your applications have `kms:Decrypt` permissions on the new key\. 

To edit the replica secret encryption key, use the following steps:

1. Log in to the Secrets Manager at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\. 

1. Choose the primary secret that you want to edit the replica secret encryption key\.

1. In the **Replication Configuration** section, choose the replica secret\.

1. From the **Actions** menu, choose **Edit encryption key**\.

1. Choose to edit the existing encryption key or add a new key\.

1. Type the name of the AWS Region into the **AWS Region** field\.

   By typing in the name of the AWS Region, you confirm changing the encryption key\.

1. Choose **Re\-encrypt Secret**\.