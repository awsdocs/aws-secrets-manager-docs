# Tutorial: Storing and Retrieving a Secret<a name="tutorials_basic"></a>

In this tutorial, you create a secret and store it in AWS Secrets Manager\. You then retrieve it in both the AWS Management Console and the AWS CLI\.

**[Step 1: Create and Store Your Secret in AWS Secrets Manager](#tutorial-basic-step1)**  
In this step, you create a secret and provide the basic information required by AWS Secrets Manager\.

**[Step 2: Retrieve Your Secret from AWS Secrets Manager ](#tutorial-basic-step2)**  
Next, you use the Secrets Manager console and the AWS CLI to retrieve the decoded secret\.

## Prerequisites<a name="tut-basic-prereqs"></a>

This tutorial assumes you have access to an AWS account, and you can sign in to AWS as an IAM user with permissions to create and retrieve secrets in the AWS Secrets Manager console, or use equivalent commands in the AWS CLI\.

## Step 1: Create and Store Your Secret in AWS Secrets Manager<a name="tutorial-basic-step1"></a>

In this step, you sign in as an IAM user and create a secret\. 

**Creating and storing your secret**

1. Sign in to the AWS Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On either the service introduction page or the **Secrets** list page, choose **Store a new secret**\.

1. On the **Store a new secret** page, choose **Other type of secret**\. 

1. Under **Specify key/value pairs to be stored in the secret**, for **Secret name**, type **tutorials/MyFirstTutorialSecret**\. This stores your secret in the virtual folder **tutorials**\.

1. Choose **Plaintext** to see the JSON version of the secret text stored in the `SecretString` field of the secret\.

1. Choose **\+Add row** to add a second key\-value pair\.

1. For **Select the encryption key**, choose **DefaultEncryptionKey**\. AWS KMS doesn't charge a fee if you use the default AWS managed key Secrets Manager creates in your account\. If you choose to use a custom KMS key, then you can be charged at the standard AWS KMS rate\.

1. Choose **Next**\.

1. Under **Secret name**, type a name for the secret in the text field\. You must use only alphanumeric characters and the characters /\_\+=\.@\-\.

1. In the **Description** field, type a description of the secret\.

   For **Description**, type, for example, **The secret I created for the first tutorial\.**

1. In the **Tags** section, add desired tags in the **Key** and **Value \- optional** text fields\.

   For this tutorial, you can leave tags blank\.

1. Choose **Next**\.

1. In this tutorial, skip configuring rotation, so choose **Disable automatic rotation**, and then choose **Next**\.

1. On the **Review** page, you can check your selected settings\. Also, be sure to review the **Sample code** section with cut\-and\-paste–enabled code you can add to your own applications and use this secret to retrieve the credentials\. Each tab has the code in different programming languages\.

1. To save your changes, choose **Store**\.

    Secrets Manager console returns to the list of secrets in your account with your new secret now included in the list\.

## Step 2: Retrieve Your Secret from AWS Secrets Manager<a name="tutorial-basic-step2"></a>

In this step, you retrieve the secret by using the Secrets Manager console and the AWS CLI\.

**To retrieve your secret in the AWS Secrets Manager console**

1. If not already logged into the console, go to the console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/) and log into the Secrets Manager service\.

1. On the **Secrets** list page, choose the name of the new secret you created\.

   Secrets Manager displays the **Secrets details** page for your secret\.

1. In the **Secret value** section, choose **Retrieve secret value**\.

1. You can view your secret as either key\-value pairs, or as a JSON text structure\.

**To retrieve your secret by using the AWS Secrets Manager CLI**

1. Open a command prompt to run the AWS CLI\. If you haven't installed the AWS CLI yet, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\. 

1. Using credentials with permissions to access your secret, type each of the following commands\.

   **To see all of the details of your secret except the encrypted text:**

   ```
   $ aws secretsmanager describe-secret --secret-id tutorials/MyFirstTutorialSecret
   {
       "ARN": "arn:aws:secretsmanager:region:123456789012:secret:tutorials/MyFirstTutorialSecret-jiObOV",
       "Name": "tutorials/MyFirstTutorialSecret",
       "Description": "My First Secret",
       "LastChangedDate": 1522680794.8,
       "LastAccessedDate": 1522627200.0,
       "VersionIdsToStages": {
           "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE": [
               "AWSCURRENT"
           ]
       }
   }
   ```

    Review the **VersionIdsToStages** response value\. The output contains a list of all of the active versions of the secret and the staging labels attached to each version\. In this tutorial, you should see one version ID \(a UUID type value\) maps to a single staging label `AWSCURRENT`\.

   **To see the encrypted text in your secret:**

   ```
   $ aws secretsmanager get-secret-value --secret-id tutorials/MyFirstTutorialSecret --version-stage AWSCURRENT
   {
       "ARN": "arn:aws:secretsmanager:region:123456789012:secret:tutorials/MyFirstTutorialSecret-jiObOV",
       "Name": "tutorials/MyFirstTutorialSecret",
       "VersionId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE",
       "SecretString": "{\"username\":\"myserviceusername\",\"password\":\"MyVerySecureP@ssw0rd!\"}",
       "VersionStages": [
           "AWSCURRENT"
       ],
       "CreatedDate": 1522680764.668
   }
   ```

   You need to include the `--version-stage` parameter in the previous command only if you want details for a version with a different staging label than `AWSCURRENT`\. Secrets Manager uses `AWSCURRENT` as the default value\.

   The result includes the JSON version of your secret value in the `SecretString` response field\.

**Summary**  
This tutorial demonstrated how easy you can create a simple secret, and to retrieve the secret value when you need the value\. For another tutorial on creating a secret and configuring automatic rotation, see [Tutorial: Rotating a Secret for an AWS Database](tutorials_db-rotate.md)\.