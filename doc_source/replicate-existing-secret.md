# Creating a replica secret from an existing secret<a name="replicate-existing-secret"></a>

You can set up an existing secret for replication across multiple Regions\. You can keep specific secrets regional by setting up an IAM policy that restricts permissions to the replication APIs\.

Before you can add replicas in Regions to an existing secret, you must enable the Regions for the replica secrets\. For more information, see [Managing AWS Regions\.](https://docs.aws.amazon.com/general/latest/gr/rande-manage.html#rande-manage-enable)

**Using the Secrets Manager console**

1. Log in to the Secrets Manager at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\. 

1. Choose an existing secret from the list of available secrets and display **Secret details**

1. Choose **Replicate secret to other regions**\.

1. Select the AWS Region from the list\.

1. Select the encryption key used to encrypt the secret\. You can use the same encryption key or a different encryption key\.

1. Choose **Complete adding regions**\.