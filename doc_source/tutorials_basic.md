# Tutorial: Creating and Retrieving a Secret<a name="tutorials_basic"></a>

In this tutorial, you create a secret and store it in AWS Secrets Manager\. You then retrieve the secret using the AWS Management Console or the AWS CLI\. 

Users new to Secrets Manager can benefit from enrolling in the the 30 day free trial and not receive billing for the activity performed in this tutorial\.

**[Step 1: Create and Store Your Secret in AWS Secrets Manager](#tutorial-basic-step1)**  
In this step, you create a secret and provide the basic information required by AWS Secrets Manager\.

**[Step 2: Retrieving Your Secret from AWS Secrets Manager ](#tutorial-basic-step2)**  
Next, you use the Secrets Manager console and the AWS CLI to retrieve the secret\.

## Prerequisites<a name="tut-basic-prereqs"></a>

This tutorial assumes you can access an AWS account, and you can sign in to AWS as an IAM user with permissions to create and retrieve secrets in the AWS Secrets Manager console, or use equivalent commands in the AWS CLI\. For more information on configuring IAM users, refer to the [IAM documentation\.](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)

## Step 1: Create and Store Your Secret in AWS Secrets Manager<a name="tutorial-basic-step1"></a>

------
#### [ Secrets Manager Console ]

**Creating and storing your secret from the console**

1. Sign in to the AWS Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On either the service introduction page or the **Secrets** list page, choose **Store a new secret**\.

1. On the **Store a new secret** page, choose **Other type of secret**\. You choose this type of secret because your secret doesn't apply to a database\.

1. Under **Specify key/value pairs to be stored in the secret**, in the first field, type **MyFirstSecret**\. To configure a password, add a value in the next field\. 

    You provide your secret information, such as credentials and connection details, as key name and value string pairs\. For example, you could specify “UserName” as a key name and the user sign\-in name as the value\.

1. For **Select the encryption key**, choose **DefaultEncryptionKey**\. Secrets Manager always encrypts the secret when you select this option, and provides it at no charge to you\. If you choose to use a custom KMS key, then AWS charges you at the standard AWS KMS rate\.

   Secrets Manager uses a unique encryption key that resides within the account and can only be used with Secrets Manager in the same region\.

1. Choose **Next**\.

1. Under **Secret name**, type a name for the secret in the text field\. You must use only alphanumeric characters and the characters /\_\+=\.@\-\.

   For example, you can use a secret name such as **tutorials/MyFirstSecret**\. This stores your secret in the virtual folder **tutorials** with the value **MyFirstSecret**\. We recommend naming secrets in a hierarchichal manner which makes managing your secrets easier\.

1. In the **Description** field, type a description of the secret\.

   For **Description**, type, for example, **Basic Create Secret**

1. In the **Tags** section, add desired tags in the **Key** and **Value \- optional** text fields\.

   For this tutorial, you can leave tags blank\. However, we recommend using tags as a best practice to help identify secrets\.

1. Choose **Next**\.

1. In this tutorial, choose **Disable automatic rotation**, and then choose **Next**\.
**Note**  
The next tutorial describes rotating a secret\.

1. On the **Review** page, you can check your secret settings\. Also, be sure to review the **Sample code** section with cut\-and\-paste–enabled code you can add to your own applications and use this secret to retrieve the credentials\. Each tab displays the code in different programming languages\.

1. To save your changes, choose **Store**\.

    Secrets Manager console returns to the list of secrets in your account with your new secret now included in the list\.

------
#### [ Secrets Manager CLI ]

1. Open a command prompt to run the AWS CLI\. If you haven't installed the AWS CLI yet, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\. 

1. **Creating your secret**

   Type the following command and parameters:

   ```
   $ aws secretsmanager create-secret --name tutorials/MyFirstSecret 
             --description "Basic Create Secret" --secret-string S3@tt13R0cks
   ```

The output of the command displays the following information:

```
{
    "ARN": "arn:aws:secretsmanager:us-west-2:123456789012:secret:tutorials/MyFirstSecret-rzM8Ja",
    "Name": "MyFirstSecret",
    "VersionId": "35e07aa2-684d-42fd-b076-3b3f6a19c6dc"
}
```

------

### Step 2: Retrieving Your Secret from AWS Secrets Manager<a name="tutorial-basic-step2"></a>

In this step, you retrieve the secret by using the Secrets Manager console and the AWS CLI\.

**Retrieving your secret in the AWS Secrets Manager console**

1. If not already logged into the console, go to the console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/) and log into the Secrets Manager service\.

1. On the **Secrets** list page, choose the name of the new secret you created\.

   Secrets Manager displays the **Secrets details** page for your secret\.

1. In the **Secret value** section, choose **Retrieve secret value**\.

1. You can view your secret as either key\-value pairs, or as a JSON text structure\.

**To retrieve your secret by using the AWS Secrets Manager CLI**

1. Open a command prompt to run the AWS CLI\. If you haven't installed the AWS CLI yet, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\. 

1. Using credentials with permissions to access your secret, type the following command and parameters\.

   **To view all of the details of your secret except the encrypted text:**

   ```
   $ aws secretsmanager describe-secret --secret-id tutorials/MyFirstSecret
   {
       "ARN": "&region-arn;secretsmanager:region:123456789012:secret:tutorials/MyFirstSecret-jiObOV",
       "Name": "tutorials/MyFirstSecret",
       "Description": "Basic Create Secret",
       "LastChangedDate": 1522680794.8,
       "LastAccessedDate": 1522627200.0,
       "VersionIdsToStages": {
           "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE": [
               "AWSCURRENT"
           ]
       }
   }
   ```

    Review the **VersionIdsToStages** response value\. The output contains a list of all active versions of the secret and the staging labels attached to each version\. In this tutorial, you should see a single version ID \(a UUID type value\) mapping to a single staging label `AWSCURRENT`\.

   **To view the encrypted text in your secret:**

   ```
   $ aws secretsmanager get-secret-value --secret-id tutorials/MyFirstSecret --version-stage AWSCURRENT
   {
       "ARN": "&region-arn;secretsmanager:region:123456789012:secret:tutorials/MyFirstSecret-jiObOV",
       "Name": "tutorials/MyFirstSecret",
       "VersionId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE",
       "SecretString": "{\"username\":\"myserviceusername\",\"password\":\"S3@ttl3R0cks\"}",
       "VersionStages": [
           "AWSCURRENT"
       ],
       "CreatedDate": 1522680764.668
   }
   ```

   If you want details for a version with a different staging label than `AWSCURRENT`, you must include the `--version-stage` parameter in the previous command\. Secrets Manager uses `AWSCURRENT` as the default value\.

   The rest of the output includes the JSON version of your secret value in the `SecretString` response field\.

**Summary**  
This tutorial demonstrated how easily you can create a simple secret, and to retrieve the secret value when you need it\. For another tutorial on creating a secret and configuring automatic rotation, see [Tutorial: Rotating a Secret for an AWS Database](tutorials_db-rotate.md)\.