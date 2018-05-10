# Tutorial: Storing and Retrieving a Secret<a name="tutorials_basic"></a>

In this tutorial, you create a secret and store it in AWS Secrets Manager\. You then retrieve it in both the AWS Management Console and the AWS CLI\.

**[Step 1: Create and Store Your Secret in AWS Secrets Manager](#tutorial-basic-step1)**  
In this step, you create a secret and provide the basic information that AWS Secrets Manager requires\.

**[Step 2: Retrieve Your Secret from AWS Secrets Manager ](#tutorial-basic-step2)**  
Next, you use the Secrets Manager console and the AWS CLI to retrieve the decoded secret\.

## Prerequisites<a name="tut-basic-prereqs"></a>

This tutorial assumes that you have access to an AWS account\. It also assumes that you can sign in to AWS as an IAM user with permissions to create and retrieve secrets in the AWS Secrets Manager console, or use equivalent commands in the AWS CLI\.

## Step 1: Create and Store Your Secret in AWS Secrets Manager<a name="tutorial-basic-step1"></a>

In this step, you sign in as an IAM user and create a secret\. 

**To create and store your secret**

1. Sign in to the AWS Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On either the service introduction page or the secrets list page, choose **Store a new secret**\.

1. On the **Store a new secret** page, choose **Other type of secret**\. 

1. For **Select the encryption key**, choose **DefaultEncryptionKey**\. You aren't charged by AWS KMS if you use the default AWS managed key that Secrets Manager creates in your account\. If you choose to use a KMS key that you created, then you can be charged at the standard AWS KMS rate\.

1. Under **Credentials you want to store**, choose **Secret key : Secret value** so that you can type the secret as key\-value pairs\.

1. In the first text box, type **username**\. In the second box, type: **myserviceusername**\.

1. Choose **\+Add row** to add a second key\-value pair\.

1. In the first box, type **password**\. In the second box, type: **MyVerySecureP@ssw0rd\!**\.

1. Choose **Plaintext** above the boxes to see the JSON version of the secret text that will be stored in the `SecretString` field of the secret\.

1. For **Select the encryption key**, leave it set at the default value **DefaultEncryptionKey**\.

1. Choose **Next**\.

1. Under **Secret name and description**, for **Secret name**, type **tutorials/MyFirstTutorialSecret**\. This stores your secret in the virtual folder "tutorials"\.

1. For **Description**, type something like: **The secret I created for the first tutorial\.**

1. Choose **Next**\.

1. In this tutorial, we don't use rotation, so choose **Disable automatic rotation**, and then choose **Next**\.

1. On the **Review** page, you can check all of the settings you chose\. Also, be sure to review the **Sample code** section that has cut\-and\-paste–enabled code that you can put into your own apps to use this secret to retrieve the credentials\. Each tab has the same code in different programming languages\.

1. To save your changes, choose **Store**\.

   You're returned to the list of secrets in your account with your new secret now included in the list\.

## Step 2: Retrieve Your Secret from AWS Secrets Manager<a name="tutorial-basic-step2"></a>

In this step, you retrieve the secret by using both the Secrets Manager console and the AWS CLI\.

**To retrieve your secret in the AWS Secrets Manager console**

1. On the secrets list page, choose the name of the new secret that you created in the previous section\.

   The details page for your secret appears\.

1. In the **Credential data** section, choose **Retrieve secret value**\.

1. You can view your secret as either key\-value pairs, or as a JSON text structure\.

**To retrieve your secret by using the AWS Secrets Manager CLI**

1. Open a command prompt where you can run the AWS CLI\. If you haven't installed the AWS CLI yet, see [Installing the AWS Command Line Interface](http://docs.aws.amazon.com/cli/latest/userguide/auth-and-access.xmlinstalling.html)\. 

1. Using credentials that have permissions to access your secret, type each of the following commands\.

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

   One item to pay attention to is the **VersionIdsToStages** response value\. This contains a list of all of the active versions of the secret and the staging labels that are attached to each version\. In this tutorial, you should see one version ID \(a UUID type value\) that maps to a single staging label `AWSCURRENT`\.

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

   Actually, you need to include the `--version-stage` parameter in the previous command only if you want details for a version with a different staging label than `AWSCURRENT`\. `AWSCURRENT` is the default value\.

   The result includes the JSON version of your secret value in the `SecretString` response field\.

**Summary**  
This tutorial demonstrated how easy it is to create a simple secret, and to retrieve the secret value whenever it's needed\. For another tutorial that shows how to create a secret and configure it to automatically rotate, see [Tutorial: Rotating a Secret for an AWS Database](tutorials_db-rotate.md)\.