# Tutorial: Rotating a User Secret with a Master Secret<a name="tutorials_db-rotate-master"></a>

This tutorial builds on the tasks you completed in the first tutorial: [Tutorial: Rotating a Secret for an AWS Database](tutorials_db-rotate.md)\. Complete the previous tutorials before beginning this one\. 

In this tutorial, you use the secret you already created as the master user for the database\. You create a new limited user, and create a secret for the user\. You then configure rotation for the user secret by using the credentials in the master secret\. The Lambda rotation function for a master secret clones the first user, and then alternates between the users, changing the password for each in turn\. 

**[Step 1: Creating a New User for Your Database and a User Secret](#tut-db-rotate-master-step1)**  
First, create a new limited\-permissions user on your Amazon RDS MySQL database and store those credentials in a new secret\.

**[Step 2: Validate Your Initial Secret](#tut-db-rotate-master-step2)**  
In step 2, confirm that you can access the database as your new user by using the credentials stored in the secret\.

**[Step 3: Configure Rotation for Your Secret](#tut-db-rotate-master-step3)**  
In step 3, configure rotation for your user secret\. You specify the master secret to use for granting access to the rotation function\.

**[Step 4: Verify Successful Rotation](#tut-db-rotate-master-step4)**  
In this step, rotate the secret twice to show the secret retrieves working credentials from two alternating users with access the database\.

**[Step 5: Clean Up](#tut-db-rotate-master-step5)**  
In the final step, remove the Amazon RDS database instance and the secrets you created to avoid incurring any unnecessary costs\.

## Prerequisites<a name="tut_db-rotate-prereqs"></a>
+ This tutorial assumes you have access to an AWS account, and you can sign in to AWS as a user with full permissions to configure AWS Secrets Manager and Amazon RDS, in either the console or by using the equivalent commands in the AWS CLI\.
+ You must complete the steps in the tutorial [Tutorial: Rotating a Secret for an AWS Database](tutorials_db-rotate.md), without deleting the database and user as described in the final section and provides you with the following items:
  + An Amazon RDS MySQL database called **MyTestDatabase** running in an instance called **MyTestDatabaseInstance**\.
  + A master user named **adminuser** with administrative permissions\.
  + A secret named **adminuser** with the credentials stored in Secrets Manager\.

## Step 1: Creating a New User for Your Database and a User Secret<a name="tut-db-rotate-master-step1"></a>

From the original tutorial, you have an Amazon RDS MySQL database with a single admin *master* user\. You also have a secret you can use to retrieve the latest credentials for the master user\. In this step, you create a new, limited user and store the credentials in a secret\. This secret could be used by, for example, a mobile app querying for information from the database\. The user doesn't require any other permissions\.

1. Open your MySQLWorkbench and choose your **MyTestDatabaseInstance** connection and log into the database\.

1. In the **Query 1** interface, enter **CREATE USER mytestuser IDENTIFIED BY '*userpassword*';**\.

1. Run the query using the **![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/run-query.png)** icon\.

   Review **Action Output** to see a successful completion of the query\.

1. Delete the previous query from **Query 1**\.

1. Enter the following text in **Query 1**: **GRANT SELECT on \*\.\* TO mytestuser**, and run the query\.

1. Review **Action Output** to see a successful completion of the query\.

### Creating a New Secret Using the Console<a name="create-secret-console"></a>

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the page with the list of secrets in your account, choose **Store a new secret**\.

1. On the **Create Secret** page, in the **Select secret type** section, choose **Credentials for RDS database**\.

1. For **User name**, type **mytestuser** to match the name of the user you created in Step 1b\.

1. For **Password**, type the same password you provided for **mytestuser** in Step 1b\.

1. For **Select the encryption key**, leave it set to **DefaultEncryptionKey**\.

1. For **Select which RDS database this secret will access**, choose the instance **MyTestDatabaseInstance** you created in the previous tutorial\.

1. Choose **Next**\.

1. In the **Secret name and description** section, for **Secret name** type **MyTestDatabaseUserSecret**\.

1. In the **Configure automatic rotation** section, leave rotation disabled for now\. Choose **Next**\.

1. In the **Review** section, verify your details and then choose **Store**\.

   You return to the list of secrets, which now includes your new secret\.

## Step 2: Validate Your Initial Secret<a name="tut-db-rotate-master-step2"></a>

Before you configure your secret to rotate automatically, you should verify you have the correct information in your secret and can connect to the database\. In the previous tutorial, you installed the MySQLWorkbench client component\. Continue to use the MySQLWorkbench client in this tutorial\.

You can retrieve the secret by using either the AWS CLI or the Secrets Manager console\. Cut and paste the user name and password into your MySQL database client\. 

1. Open your MySQLWorkbench and choose the **\+** to create a new connection\.

1. For the **Connection Name**, enter **MyTestUser**\.

1. Copy and paste your MySQL host name into **Hostname**\. Leave the port at `3306`

1. For the **Username**, enter **mytestuser**, and choose **Test Connection**\.

1. MySQLWorkbench returns a message indicating you successfully connected to the database\.

## Step 3: Configure Rotation for Your Secret<a name="tut-db-rotate-master-step3"></a>

After you validate the initial credentials in your secret, you can configure and start your first rotation\.

1. In the Secrets Manager console, choose the secret **MyTestDatabaseUserSecret**\.

1. On the secret details page, in the **Rotation configuration** section, choose **Edit rotation**\.

1. On the **Edit rotation configuration** page, choose **Enable automatic rotation**\.

1. For **Select rotation interval**, choose **30 days**\.

1. Choose a Lambda function from the list\. 

1. Under **Select which secret will be used to perform the rotation**, choose **Use a secret that I have previously stored in AWS Secrets Manager**\.

1. In the list of secrets that appears, choose **MyTestDatabaseMasterSecret**\.

1. Choose **Save**\.

## Step 4: Verify Successful Rotation<a name="tut-db-rotate-master-step4"></a>

Now you have rotated the secret, you can confirm that the new credentials in the secret work to connect with your database\.

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the **Secrets** list page, choose the name of your user secret\.

1. Choose **Retrieve secret value** and view your current password\. The secret value should be either the original password or a new one created by a successful rotation\. If you still see the original password, close the **Secret value** section and reopen it until the secret value successfully changes\. This step might take a few minutes\. 

1. When the new secret appears, copy and paste it into the MySQLWorkbench **MyTestUser**connection\.

1. MySQL Workbench returns a message indicating you successfully logged into the database with the new secret\.

## Step 5: Clean Up<a name="tut-db-rotate-master-step5"></a>

Because databases and secrets can incur charges on your AWS bill, you should remove the database instance and the secret you created in this tutorial\.

**Deleting the secret**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the list of secrets, choose the **MyTestDatabaseSecret** secret you created for this tutorial\.

1. Choose **Actions**, and then choose **Delete secret**\.

1. In the **Schedule secret deletion** dialog box, for **Enter a waiting period**, type **7**, the minimum value allowed\.

1. Choose **Schedule deletion**\.

   After the number of days in the recovery window elapses, Secrets Manager removes the secret permanently\.

**To delete the database instance**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.

1. In the list of available instances, choose the **MyTestDatabaseInstance** instance you created for this tutorial\.

1. Choose **Instance actions**, and then choose **Delete**\.

1. On the **Delete DB Instance** page, in the **Options** section, for **Create final snapshot**, choose **No**\.

1. Select the acknowledgement that you lose all of your data, and then choose **Delete**\.