# Tutorial: Create and retrieve a secret in AWS Secrets Manager<a name="tutorials_basic"></a>

In this tutorial, you create a secret in AWS Secrets Manager that contains a single string\. You then retrieve the secret string using the console\. To create other types of secrets, see [Create a secret](manage_create-basic-secret.md)\.

**Contents**
+ [Permissions](#tutorials_basic-permissions)
+ [Step 1: Create a secret](#tutorial-basic-step1)
+ [Step 2: Retrieve a secret](#tutorial-basic-step2)
+ [Step 3: Cleanup resources](#tutorials_basic-step-cleanup)
+ [Related resources](#tutorials_basic-step-next)

## Permissions<a name="tutorials_basic-permissions"></a>

This tutorial assumes you have an AWS account, and you can sign in to AWS as an IAM user with the following permissions:
+ `secretsmanager:CreateSecret`
+ `secretsmanager:ListSecrets`
+ `secretsmanager:GetSecretValue`

For more information, see [Attach a permissions policy to an identity](auth-and-access_iam-policies.md)\.

## Step 1: Create a secret<a name="tutorial-basic-step1"></a>

To create a secret, you can use the console to enter the secret details\. In this tutorial, the secret is a single string\. 

**To create a secret**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Under **Secrets**, choose **Store a new secret**\.

1. On the **Store a new secret** page, do the following:

   1. For Secret type, choose **Other type of secret**\. 

   1. For **Key/value pairs**, in the first field, enter **Password**\. In the second field, enter a password\. This will be encrypted when you save the secret\.

   1. For **Encryption key**, keep **DefaultEncryptionKey** to use the AWS managed key for Secrets Manager\. There is no cost for using this key\.

   1. Choose **Next**\.

1. On the **Secret name and description** page, for **Secret name**, enter **TutorialSecret**, and then at the bottom of the page, choose **Next**\.

1. On the **Secret rotation ** page, keep **Disable automatic rotation**, and then at the bottom of the page, choose **Next**\.

1. On the **Review** page, review the secret details, and then choose **Store**\.

    Secrets Manager console returns to the list of secrets in your account and the new secret is now in the list\.

## Step 2: Retrieve a secret<a name="tutorial-basic-step2"></a>

Now that you've stored a secret, you can retrieve it from Secrets Manager using the console\. For other ways of retrieving secrets, see [Retrieve secrets from AWS Secrets Manager in code](retrieving-secrets.md)\.

**To retrieve a secret**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the **Secrets** list page, choose **TutorialSecret**\. 

1. On the **Secrets details** page, in the **Secret value** section, choose **Retrieve secret value**\.

   You can view your secret as a key value pair or on the **Plaintext** tab as JSON\.

## Step 3: Cleanup resources<a name="tutorials_basic-step-cleanup"></a>

To avoid potential charges, delete the secret you created in this tutorial\.

**To delete a secret**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the list of secrets, select **TutorialSecret**, choose **Actions**, and then choose **Delete**\.

1. In the **Disable secret and schedule deletion** dialog box, in **Waiting period**, enter **7**, the minimum number of days to wait before the deletion becomes permanent\. You can't delete a secret immediately by using the console\. To delete a secret immediately, you must use the [AWS CLI](manage_delete-secret.md#manage_delete-secret_cli)\.

1. Choose **Schedule deletion**\.

## Related resources<a name="tutorials_basic-step-next"></a>

For more information, see:
+ [Create and manage secrets with AWS Secrets Manager](managing-secrets.md)
+ [Retrieve secrets from AWS Secrets Manager in code](retrieving-secrets.md)