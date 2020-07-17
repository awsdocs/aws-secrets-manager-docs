# Tutorial: Rotating a Secret for an AWS Database<a name="tutorials_db-rotate"></a>

In this tutorial, you create a secret for an AWS database and configure the secret to rotate on a schedule\. You trigger one rotation manually, and then confirm that the new version of the secret continues to provide access\.

**[Configuring a Test MySQL Database](#tut-db-rotate-step1)**  
In this step, create a test database in Amazon Relational Database Service \(Amazon RDS\)\. For this tutorial, the test database runs MySQL\.

**[Step 2: Create Your Secret](#tut-db-rotate-step2)**  
Next, use the Secrets Manager console to create your secret and populate the secret with the initial user name and password for your MySQL database\. Test the secret by using the returned credentials to sign in to the database\.

**[Step 3: Validate Your Initial Secret](#tut-db-rotate-step3)**  
In Step 3, use your new secret to test the credentials and ensure you can use them to connect to your database\.

**[Step 4: Configure Rotation for Your Secret](#tut-db-rotate-step4)**  
In Step 4, enable rotation for the secret and perform the initial rotation\.

**[Step 5: Verify Successful Rotation](#tut-db-rotate-step5)**  
In this step, after the initial rotation completes, repeat the validation steps to show that the new credentials generated during rotation continue to allow you to access the database\.

**[Step 6: Clean Up](#tut-db-rotate-step6)**  
In the final step, remove the Amazon RDS database instance and the secret to avoid incurring any unnecessary costs\.

## Prerequisites<a name="tut_db-rotate-prereqs"></a>

The tutorial assumes you can access an AWS account, and you can sign into AWS as a user with full permissions to configure AWS Secrets Manager and Amazon RDS, either using the console or the equivalent commands in the AWS CLI\.

The tutorial uses a MySQL client tool, MySQLWorkbench, to interact with the database, configure users, and check status\. The tutorial includes the installation instructions at the appropriate point in the following steps\.

The database configured in this tutorial allows access to the public internet on port 3306, again for simplicity in setup for the tutorial\. To complete this tutorial, you must be able to access the MySQL database from your internet\-connected computer by using the MySQL client tool, MySQLWorkbench\. We recommend you follow the guidance in the Lambda and Amazon EC2 VPC documentation to configure production servers securely\.

**Important**  
For rotation to work, your network environment must permit the Lambda rotation function to communicate with your database and the Secrets Manager service\. Because this tutorial configures your database with public internet access, Lambda automatically configures your rotation function to access the database through the public IP address\. If you block public internet access to your database instance, then you must configure the Lambda function to run in the same VPC as the database instance\. Then you must either [configure your VPC with a private Secrets Manager endpoint](rotation-network-rqmts.md), or [configure the VPC with public internet access by using a NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html), so that the Lambda rotation function can access the public Secrets Manager endpoint\.

## Required Permissions<a name="tut_db-rotate-perms"></a>

To successfully run this tutorial, you must have all of the permissions associated with the [SecretsManagerReadWrite AWS managed policy](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/SecretsManagerReadWrite)\. You must also have permission to create an IAM role and attach a permission policy to the role\. You can grant either the [IAMFullAccess AWS managed policy](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/IAMFullAccess), or explicitly assign `iam:CreateRole` and `iam:AttachRolePolicy`\. 

**Warning**  
The `iam:CreateRole` and `iam:AttachRolePolicy` permit a user to grant themselves any permissions, so grant these policies only to trusted users in an account\.

## Configuring a Test MySQL Database<a name="tut-db-rotate-step1"></a>

1. for this part of the tutorial, sign in to your account and configure a MySQL database in Amazon RDS\.

1. Perform the following steps:

   1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\. 

   1. From the **Dashboard**, scroll down to the **Create database** section, and choose **Create database**\.

   1. Refer to the Amazon RDS tutorial, [Creating a MySQL DB Instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.MySQL.html), for the latest information on setting up an RDS database\.

      Use the following information when creating your database:
      + DB instance identifier: **MyTestDatabaseInstance**\.
      + Master username: **adminuser**\.
      + Master password: Type a secure initial password, and retype the password in the **Confirm password**box\. *Be sure to remember this password\.* You need it when you create your secret in Step 2\.
**Note**  
Database creation may take up to 20 minutes before the DB instance becomes available\. 

   1. From the list of available databases under **RDS > Databases**, choose your database and then choose **Modify**\.

   1. In the **Network and Security** section, set the **Public accessibility** to **Yes**\.

   1. In the **Backup** section, set the **Backup retention period** to **0 days** to disable backups\. 

   1. Leave the rest of the settings at the default values\.

   1. Choose **Continue**\.

   1. In the **Scheduling of modifications** section, choose **Apply immediately** and then **Modify DB Instance**\.

   1. When the **Summary** section displays **Available** under **Info**, refresh the page, and then scroll down to the **Connectivity and security** section\.

   1. In the **Security** section, choose the **default** under **VPC security groups**\. The console for Amazon EC2 opens and displays the configured **Security Groups**\.

   1. Choose **Inbound rules** and then choose **Edit Inbound Rules**\.

   1. Under **Source type**, choose **Anywhere**, and choose **Save rules**\.

**Note**  
To configure the tutorial correctly, use these settings at a minimum\. If you require a private VPC, then the Lambda function must be configured to run in that VPC\. Next, you must either configure your VPC with a [private Secrets Manager endpoint](rotation-network-rqmts.md) or configure the VPC with public internet access by using a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)\. These configurations allow the Lambda rotation function to access the public Secrets Manager endpoint\.

## Step 2: Create Your Secret<a name="tut-db-rotate-step2"></a>

In this step, you create a secret in Secrets Manager, and populate the secret with your test details, which include database and the credentials of your master user\. 

**To create your secret**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Ensure you set your console to the same region as you created the Amazon RDS MySQL database in the previous step\.

1. Choose **Store a new secret**\.

1. On the **Store a new secret** page, in the **Select secret type** section, choose **Credentials for RDS database**\.

1. For **User name**, type **adminuser** to match the name of the master user you previously provided in Step 1\.3\.

1. For **Password**, type the same password that you provided for **adminuser** when you created your database\.

1. For **Select the encryption key**, leave it set to **DefaultEncryptionKey**\. AWS bills your account if you use a custom master key \(CMK\) instead of the default CMK\.

1. For **Select which RDS database this secret will access**, and choose the instance **MyTestDatabaseInstance** you created in Step 1\. Choose **Next**\.

1. In the **Secret name and description** section, for **Secret name**, type **MyTestDatabaseMasterSecret**\. Choose **Next**\.

1. In the **Configure automatic rotation** section, disable rotation for now\. Choose **Next**\.

1. In the **Review** section, verify your details, and then choose **Store**\.

   Secrets Manager returns to the list of secrets, which now includes your new secret\.

## Step 3: Validate Your Initial Secret<a name="tut-db-rotate-step3"></a>

Before you configure your secret to rotate automatically, you should verify you have the correct information in your secret and can connect to the database\. This tutorial describes how to install a GUI\-based application, **MySQL Workbench** to test the connection\. [Download](https://dev.mysql.com/downloads/workbench/) the client appropriate to your operating system\.

At the very least, you can retrieve the secret by using either the AWS CLI or the Secrets Manager console\. Then cut and paste the user name and password into the MySQL database client\.

To test your database your database connection

1. After installing the MySQLWorkBench client software, open the MySQLWorkbench client to display the **Welcome to MySQLWorkbench** interface\.

   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/mysql-welcome-small.png)

1. In the **MySQL Connections**, choose the **\+** icon to display **Setup New Connection**\.

   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/connect-host-small.png)

1. For Connection Name, enter **MyTestDatabaseInstance**\.

1. For the **Hostname**, enter the endpoint of the database, such as **MyTestDatabase\.*hostname*\.region\.rds\.amazonaws\.com**\.

   You can find the endpoint on the details page for your database\. In the Amazon RDS console, choose **Databases** section of the **RDS > Databases > MyTestDatabaseInstance**\.

1. Leave the **Port** at the default value, 3306\.

1. In the **Username** field, type the username you created for the database, **adminuser**\.

1. Choose **Test Connection** and enter the database password in the **Password** field\.

   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/password.png)

1. If configured correctly, MySQLWorkbench displays a successful connection message\.

   ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/connection-successful.png)

1. Choose **OK**\.
**Troubleshooting tip**  
If the MySQLWorkbench client fails to connect to the database, you should check the security group attached to the VPC with the database\. The default rules in a security group enable all outbound traffic, but the rules block all inbound traffic except for the traffic you explicitly allow by defining a rule\. If you run your computer on the public internet then your security group must enable inbound traffic from the Internet to the TCP port you configured your database communication, typically port 3306\. If you configure MySQL to use a different TCP port, ensure you update the security rule to match\. 

## Step 4: Configure Rotation for Your Secret<a name="tut-db-rotate-step4"></a>

After you validate the initial credentials in your secret, you can configure and start your first rotation\.

To configure secret rotation

1. In the Secrets Manager console, choose the secret **MyTestDatabaseMasterSecret**\.

1. On the **Secret details** page, in the **Rotation configuration** section, choose **Edit rotation**\.

1. On the **Edit rotation configuration** page, choose **Enable automatic rotation**\.

1. For **Select rotation interval**, choose **30 days**\.

1. Under **Select the secret will be used to perform the rotation**, choose **Use this secret**\.

1. Choose **Save**\. Secrets Manager begins to configure rotation for your secret, including creating the Lambda rotation function and attaching a role enabling Secrets Manager to invoke the function\. 

1. Stay on the console page with the **Rotation is being configured** message, until the message changes to **Your secret MyTestDatabaseMasterSecret has been successfully stored and secret rotation is enabled\.**

## Step 5: Verify Successful Rotation<a name="tut-db-rotate-step5"></a>

After you rotate the secret, you can confirm the new credentials in the secret work to connect with your database\.

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose your secret **MyTestDatabaseMasterSecret**\.

1. Choose **Retrieve secret value**\.

1. Locate the **password** field\. 

   If you successfully rotated the secret, the password should change to something similar to `E4%I)rj)vmpRg)U}++=}GHAnNDD1v0cJ` instead of the original secret\.

1. To access your database, open MySQLWorkbench and choose the connection **MyTestDatabase**\.

1. When prompted for the password, copy the password from Secrets Manager and paste into the **Password** field\. Choose **OK**\.

   You can successfully access the database with the new password and validate that rotating secrets works\.

## Step 6: Clean Up<a name="tut-db-rotate-step6"></a>

**Important**  
If you intend to also perform the tutorial [Tutorial: Rotating a User Secret with a Master Secret](tutorials_db-rotate-master.md), don't perform these steps until you complete the tutorial\.

Because databases and secrets can incur charges on your AWS bill, you should remove the database instance and the secret you created in this tutorial after you finish experimenting with the tutorial\.

**Deleting the secret**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the list of secrets, choose the **MyTestDatabaseSecret** secret you created for this tutorial\.

1. Choose **Actions**, then choose **Delete secret**\.

1. In the **Schedule secret deletion** dialog box, for **Enter a waiting period**, type **7**, the minimum value allowed\.

1. Choose **Schedule deletion**\.

   After the number of days in the recovery window elapses, Secrets Manager removes the secret permanently\.

**To delete the database instance**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.

1. In the list of available instances, choose the **MyTestDatabaseInstance** instance you created for this tutorial\.

1. Choose **Instance actions**, and then choose **Delete**\.

1. On the **Delete DB Instance** page, in the **Options** section, for **Create final snapshot**, choose **No**\.

1. Select the acknowledgement of losing all of your data, and then choose **Delete**\.