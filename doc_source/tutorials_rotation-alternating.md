# Set up alternating users rotation for AWS Secrets Manager<a name="tutorials_rotation-alternating"></a>

In this tutorial, you learn how to set up alternating users rotation for a secret that contains database credentials\. *Alternating users rotation* is a rotation strategy where Secrets Manager clones the user and then alternates which user's credentials are updated\. This strategy is a good choice if you need high availability for your secret, because one of the alternating users has current credentials to RDS while the other one is being updated\. For more information, see [Rotation strategies](rotating-secrets_strategies.md)\. 

To set up alternating users rotation, you need two secrets:
+ One secret with the credentials that you want to rotate\.
+ A second secret that has credentials for an administrator or superuser who has permissions to both change the first users's password and clone the first user\. 

**Contents**
+ [Permissions](#tutorials_rotation-alternating-permissions)
+ [Prerequisites](#tutorials_rotation-alternating-step-setup)
+ [Step 1: Create an Amazon RDS database user](#tutorials_rotation-alternating-step-database)
+ [Step 2: Create a secret for the user credentials](#tutorials_rotation-alternating_step-rotate)
+ [Step 3: Test the rotated secret](#tutorials_rotation-alternating_step-test-secret)
+ [Step 4: Clean up resources](#tutorials_rotation-alternating_step-cleanup)
+ [Next steps](#tutorials_rotation-alternating_step-next)

## Permissions<a name="tutorials_rotation-alternating-permissions"></a>

For the tutorial prerequisites, you need administrative permissions to your AWS account\. In a production setting, it is a best practice to use different roles for each of the steps\. For example, a role with database admin permissions would create the Amazon RDS database, and a role with network admin permissions would set up the VPC and security groups\. For the tutorial steps, we recommend you continue using the same identity\.

For information about how to set up permissions in a production environment, see [Authentication and access control for AWS Secrets Manager](auth-and-access.md)\.

## Prerequisites<a name="tutorials_rotation-alternating-step-setup"></a>

The prerequisite for this tutorial is [Set up single user rotation for AWS Secrets Manager](tutorials_rotation-single.md)\. Don't clean up the resources at the end of the first tutorial\. After that tutorial, you have a realistic environment with an Amazon RDS database and a Secrets Manager secret\. The secret contains admin credentials for the database, and it is set up to rotate every 10 days\. 

You also have a connection configured in MySQL Workbench to connect to the database with the admin credentials\.

## Step 1: Create an Amazon RDS database user<a name="tutorials_rotation-alternating-step-database"></a>

First, you need a user whose credentials will be stored in the secret\.

**To create a database user**

1. In MySQL Workbench, choose the connection **SecretsManagerTutorial**\. 

1.  In the **Query** window, enter the following commands \(including a strong password\) and then choose **Execute**\.

   ```
   CREATE DATABASE myDB;
   CREATE USER 'appuser'@'%' IDENTIFIED BY 'EXAMPLE-PASSWORD';
   GRANT ALL PRIVILEGES ON myDB . * TO 'appuser'@'%';
   ```

   In the **Output** window, you see the commands are successful\.

## Step 2: Create a secret for the user credentials<a name="tutorials_rotation-alternating_step-rotate"></a>

Next, you create a secret to store the credentials of the user you just created\. This is the secret you'll be rotating\. You turn on automatic rotation, and to indicate the alternating users strategy, you choose a separate superuser secret that has permission to change the first user's password\.

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose **Store a new secret**\.

1. On the **Store a new secret** page, do the following:

   1. For **Secret type**, choose **Credentials for Amazon RDS database**\.

   1. For **Credentials**, enter the username **newuser** and the password you entered for the database user you created using MySQL Workbench\.

   1. For **Database**, choose **secretsmanagertutorialdb**\.

1. On the **Secret name and description** page, for **Secret name**, enter **SecretsManagerTutorialAppuser** and then choose **Next**\.

1. On the **Secret rotation** page, do the following:

   1. Turn on **Automatic rotation**\.

   1. For **Rotation schedule**, set a schedule of **Days**: **2** Days with **Duration**: **2h**\. Keep **Rotate immediately** selected\. 

   1. For **Rotation function**, choose **Create a rotation function**, and then for the function name, enter **tutorial\-alternating\-users\-rotation**\.

   1. For **Use separate credentials**, choose **Yes**, and then under **Secrets**, choose **SecretsManagerTutorialAdmin\-*a1b2c3d4e5f6***\.

   1. Choose **Next**\.

1. On the **Review** page, choose **Store**\.

   Secrets Manager returns to the the secret details page\. At the top of the page, you can see the rotation configuration status\.

   Secrets Manager uses CloudFormation to create resources such as the Lambda rotation function and an execution role that runs the Lambda function\. When CloudFormation finishes, the banner changes to **Secret scheduled for rotation**\. The first rotation is complete\.

## Step 3: Test the rotated secret<a name="tutorials_rotation-alternating_step-test-secret"></a>

Now that the secret is rotated, you can check that the secret still contains valid credentials\. The password in the secret has changed from the original credentials\.

**To retrieve the new password from the secret**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose **Secrets**, and then choose the secret **SecretsManagerTutorialAppuser**\.

1. On the **Secret details** page, scroll down and choose **Retrieve secret value**\.

1. In the **Key/value** table, copy the **Secret value** for **password**\.

**To test the credentials**

1. In MySQL Workbench, right\-click the connection **SecretsManagerTutorial** and then choose **Edit Connection**\.

1. In the **Manage Server Connections** dialog box, for **Username**, enter **appuser**, and then choose **Close**\.

1. Back in MySQL Workbench, choose the connection **SecretsManagerTutorial**\.

1. In the **Open SSH Connection** dialog box, for **Password**, paste the password you retrieved from the secret, and then choose **OK**\.

   If the credentials are valid, then MySQL Workbench opens to the design page for the database\.

This shows that the secret rotation is successful\. The credentials in the secret have been updated and it is a valid password to connect to the database\. 

## Step 4: Clean up resources<a name="tutorials_rotation-alternating_step-cleanup"></a>

To avoid potential charges, and to remove the EC2 instance that has access to the internet, delete the following resources you created in this tutorial and its prerequisites:
+ Amazon EC2 instance\. For instructions, see [Terminate an instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html#terminating-instances-console)\.
+ Secrets Manager secret `SecretsManagerTutorialAppuser`\. See [Delete a secret](manage_delete-secret.md)\.
+ Secrets Manager endpoint\. For instructions, see [Delete a VPC endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/delete-vpc-endpoint.html)\.
+ Internet gateway\. First [Detach an internet gateway from your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#detach-igw), then [Delete an internet gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#delete-igw)\.
+ AWS CloudFormation stack\. For instructions, see [Delete a stack](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-delete-stack.html)\.

## Next steps<a name="tutorials_rotation-alternating_step-next"></a>
+ Learn how to retrieve secrets in your applications\. See [Retrieve secrets from AWS Secrets Manager](retrieving-secrets.md)\.
+ Learn how to create a secret with automatic rotation using AWS CloudFormation, see [ AWS::SecretsManager::RotationSchedule](https://docs.aws.amazon.com/https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-rotationschedule.html) in the AWS CloudFormation User Guide\.
+ Learn about other rotation schedules\. See [Schedule expressions in Secrets Manager rotation](rotate-secrets_schedule.md)\.