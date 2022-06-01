# Move hardcoded database credentials to AWS Secrets Manager<a name="hardcoded-db-creds"></a>

If you have plaintext database credentials in your code, we recommend that you move the credentials to Secrets Manager and then rotate them immediately\. Moving the credentials to Secrets Manager solves the problem of the credentials being visible to anyone who sees the code, because going forward, your code retrieves the credentials directly from Secrets Manager\. Rotating the secret updates the password and then revokes the current hardcoded password so that it is no longer valid\. 

For Amazon RDS, Amazon Redshift, and Amazon DocumentDB databases, use the steps in this page to move hardcoded credentials to Secrets Manager\. For other types of credentials and other secrets, see [Move hardcoded secrets to AWS Secrets Manager](hardcoded.md)\.

Before you begin, you need to determine who needs access to the secret\. We recommend using two IAM roles to manage permission to your secret:
+ A role that manages the secrets in your organization\. For more information, see [Secrets Manager administrator permissions](auth-and-access.md#auth-and-access_admin)\. You'll create and rotate the secret using this role\.
+ A role that can use the credentials at runtime, *RoleToRetrieveSecretAtRuntime* in this tutorial\. Your code assumes this role to retrieve the secret\.

**Topics**
+ [Step 1: Create the secret](#hardcoded-db-creds_step2)
+ [Step 2: Update your code](#hardcoded-db-creds_step3)
+ [Step 3: Rotate the secret](#hardcoded-db-creds_step5)
+ [Next steps](#hardcoded-db-creds_nextsteps)

## Step 1: Create the secret<a name="hardcoded-db-creds_step2"></a>

The first step is to copy the existing hardcoded credentials into a secret in Secrets Manager\. For the lowest latency, store the secret in the same Region as the database\. 

**To create a secret**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose **Store a new secret**\.

1. On the **Store a new secret **page, do the following:

   1. For **Secret type**, choose the type of database credentials to store:
      + **Amazon RDS database**
      + **Amazon DocumentDB database**
      + **Amazon Redshift cluster**\.
      + For other types of secrets, see [Replace hardcoded secrets ](https://docs.aws.amazon.com/secretsmanager/latest/userguide/hardcoded.html)\.

   1. For **Credentials**, enter the existing hardcoded credentials for the databse\.

   1. For **Encryption key**, choose **aws/secretsmanager** to use the AWS managed key for Secrets Manager\. There is no cost for using this key\. You can also use your own customer managed key, for example to [access the secret from another AWS account](auth-and-access_examples_cross.md)\. 

   1. For **Database**, choose your database\.

   1. Choose **Next**\.

1. On the **Secret name and description** page, do the following:

   1. Enter a descriptive **Secret name** and **Description**\. 

   1. In **Resource permissions**, choose **Edit permissions**\. Paste the following policy, which allows *RoleToRetrieveSecretAtRuntime* to retrieve the secret, and then choose **Save**\.

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
              "AWS": "arn:aws:iam::AccountId:role/RoleToRetrieveSecretAtRuntime"
            },
            "Action": "secretsmanager:GetSecretValue",
            "Resource": "*"
          }
        ]
      }
      ```

   1. At the bottom of the page, choose **Next**\.

1. On the **Secret rotation** page, keep rotation off for now\. You'll turn it on later\. Choose **Next**\.

1. On the **Review** page, review your secret details, and then choose **Store**\.

## Step 2: Update your code<a name="hardcoded-db-creds_step3"></a>

Your code must assume the IAM role *RoleToRetrieveSecretAtRuntime* to be able to retrieve the secret\. For more information, see [Switching to an IAM role \(AWS API\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-api.html)\.

Next, you update your code to retrieve the secret from Secrets Manager using the sample code provided by Secrets Manager\. 

**To find the sample code**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the **Secrets** page, choose your secret\.

1. Scroll down to **Sample code**\. Choose your language, and then copy the code snippet\. 

In your application, remove the hardcoded credentials and paste the code snippet\. Depending on your code language, you might need to add a call to the function or method in the snippet\.

Test that your application works as expected with the secret in place of the hardcoded credentials\.

## Step 3: Rotate the secret<a name="hardcoded-db-creds_step5"></a>

The last step is to revoke the hardcoded credentials by rotating the secret\. *Rotation* is the process of periodically updating a secret\. When you rotate a secret, you update the credentials in both the secret and the database\. Secrets Manager can automatically rotate a secret for you on a schedule you set\.

Part of setting up rotation is ensuring that the Lambda rotation function can access both Secrets Manager and your database\. When you turn on automatic rotation, Secrets Manager creates the Lambda rotation function in the same VPC as your database so that it has network access to the database\. The Lambda rotation function must also be able to make calls to Secrets Manager to update the secret\. We recommend that you create a Secrets Manager endpoint in the VPC so that calls from Lambda to Secrets Manager don't leave AWS infrastructure\. For more information, see [Network access for the rotation function](rotation-network-rqmts.md)\. For instructions, see [Using an AWS Secrets Manager VPC endpoint](vpc-endpoint-overview.md)\.

**To turn on rotation**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the **Secrets** page, choose your secret\.

1. On the **Secret details** page, in the **Rotation configuration** section, choose **Edit rotation**\.

1. In the **Edit rotation configuration** dialog box, do the following:

   1. Turn on **Automatic rotation**\.

   1. Under **Rotation schedule**, enter your schedule in UTC time zone\. 

   1. Choose **Rotate immediately when the secret is stored** to rotate your secret when you save your changes\.

   1. Under **Rotation function**, choose **Create a new Lambda function** and enter a name for your new function\. Secrets Manager adds "SecretsManager" to the beginning of your function name\.

   1. For **Use separate credentials to rotate this secret**, choose **No**\.

   1. Choose **Save**\.

**To check that the secret rotated**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose **Secrets**, and then choose the secret\.

1. On the **Secret details** page, scroll down and choose **Retrieve secret value**\.

   If the secret value changed, then rotation succeeded\. If the secret value didn't change, you need to [Troubleshoot rotation](troubleshoot_rotation.md) by looking at the CloudWatch Logs for the rotation function\.

Test that your application works as expected with the rotated secret\.

## Next steps<a name="hardcoded-db-creds_nextsteps"></a>

After you remove a hardcoded secret from your code, some ideas to consider next:
+ You can improve performance and reduce costs by caching secrets\. For more information, see [Retrieve secrets from AWS Secrets Manager](retrieving-secrets.md)\.
+ You can choose a different rotation schedule\. For more information, see [Schedule expressions in Secrets Manager rotation](rotate-secrets_schedule.md)\.
+ You can choose a different rotation strategy\. For more information, see [Rotation strategies](rotating-secrets_strategies.md)\.
+ To find hardcoded secrets in your Java and Python applications, we recommend [Amazon CodeGuru Reviewer](https://docs.aws.amazon.com/codeguru/latest/reviewer-ug/welcome.html)\.