# Tutorial: Create and retrieve a secret<a name="tutorials_basic"></a>

In this tutorial, you create a secret and store it in AWS Secrets Manager\. You can use either the console or the AWS CLI\. The secret contains a single password, stored as a key\-value pair\. You encrypt your secret with the AWS managed key \(`aws/secretsmanager`\)\. There is no cost for using this key\. 

You then retrieve the secret using the console or the AWS CLI\. 

Users new to Secrets Manager can benefit from enrolling in the 30 day free trial and not receive billing for the activity performed in this tutorial\.

## Prerequisites<a name="tut-basic-prereqs"></a>

This tutorial assumes you can access an AWS account, and you can sign in to AWS as an IAM user with permissions to create and retrieve secrets in the AWS Secrets Manager console, or use equivalent commands in the AWS CLI\. For more information on configuring IAM users, refer to the [IAM documentation\.](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)

## Step 1: Create and store your secret in AWS Secrets Manager<a name="tutorial-basic-step1"></a>

**To store your secret by using the console**

1. Sign in to the AWS Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose **Store a new secret**\.

1. On the **Store a new secret** page, do the following:

   1. For Secret type, choose **Other type of secret**\. 

   1. For **Key/value pairs**, in the first field, enter **MyPassword**\. In the second field, enter a password\. This is the text that will be encrypted when you store the secret\.

   1. For **Encryption key**, keep **DefaultEncryptionKey**\. 

   1. Choose **Next**\.

1. On the next page, for **Secret name**, enter **tutorial/MyFirstSecret**, and then at the bottom of the page, choose **Next**\.

1. On the next page, keep **Disable automatic rotation**, and then at the bottom of the page, choose **Next**\.

1. On the **Review** page, you can check your secret settings\.

   In **Sample code**, you can copy the code to paste in your applications\. These examples retrieve the secret value that you stored\. In this tutorial, you stored a password\. 

   Choose **Store**\.

    Secrets Manager console returns to the list of secrets in your account and your new secret is now in the list\.

**To store your secret by using the CLI**

1. Open a command prompt to run the AWS CLI\. If you haven't installed the AWS CLI, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\. 

1. Enter the following command:

   ```
   $ aws secretsmanager create-secret --name tutorial/MyFirstSecret --secret-string EXAMPLE-PASSWORD
   {
       "ARN": "arn:aws:secretsmanager:us-east-2-2:111122223333:secret:tutorial/MyFirstSecret-a1b2c3",
       "Name": "tutorial/MyFirstSecret",
       "VersionId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE"
   }
   ```

## Step 2: Retrieve your secret from AWS Secrets Manager<a name="tutorial-basic-step2"></a>

**To retrieve your secret by using the console**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the **Secrets** list page, choose **tutorial/MyFirstSecret**\. 

1. On the **Secrets details** page, in the **Secret value** section, choose **Retrieve secret value**\.

   You can view your secret as either key\-value pairs, or on the **Plaintext** tab as a JSON text structure\.

**To retrieve your secret by using the CLI**

1. Open a command prompt to run the AWS CLI\. If you haven't installed the AWS CLI, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\. 

1. Enter the following command:

   ```
   $ aws secretsmanager get-secret-value --secret-id tutorial/MyFirstSecret
   {
       "ARN": "arn:aws:secretsmanager:us-east-2:111122223333:secret:tutorial/MyFirstSecret-a1b2c3",
       "Name": "tutorial/MyFirstSecret",
       "VersionId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE",
       "SecretString": "S3@ttl3R0cks",
       "VersionStages": [
           "AWSCURRENT"
       ],
       "CreatedDate": 1522680764.668
   }
   ```