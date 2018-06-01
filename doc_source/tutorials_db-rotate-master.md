# Tutorial: Rotating a User Secret with a Master Secret<a name="tutorials_db-rotate-master"></a>

This tutorial builds on what you completed in the first tutorial: [Tutorial: Rotating a Secret for an AWS Database](tutorials_db-rotate.md)\. It requires that you complete those steps first\. 

In this tutorial, you treat the secret that you already have as the master user for the database\. You create a new limited user, and create a secret for that user\. You then configure rotation for the user secret to work by using the credentials in the master secret\. The Lambda rotation function for a master secret clones the first user, and then alternates between the users, changing the password for each in turn\. 

**[Step 1: Create a New User for Your Database and a User Secret](#tut-db-rotate-master-step1)**  
First, you create a new limited\-permissions user on your Amazon RDS MySQL database and store those credentials in a new secret\.

**[Step 2: Validate Your Initial Secret](#tut-db-rotate-master-step2)**  
In step 2, you confirm that you can access the database as your new user by using the credentials that are stored in the secret\.

**[Step 3: Configure Rotation for Your Secret](#tut-db-rotate-master-step3)**  
In step 3, you configure rotation for your user secret\. You specify that the master secret should be used to grant access to the rotation function\.

**[Step 4: Verify Successful Rotation](#tut-db-rotate-master-step4)**  
In this step, we rotate the secret twice to show that the secret retrieves working credentials from two alternating users that can access the database\.

**[Step 5: Clean Up](#tut-db-rotate-master-step5)**  
In the final step, we remove the Amazon RDS database instance and the secrets that you created to avoid incurring any unnecessary costs\.

## Prerequisites<a name="tut_db-rotate-prereqs"></a>
+ This tutorial assumes that you have access to an AWS account\. It also assumes that you can sign in to AWS as a user with full permissions to operate AWS Secrets Manager and Amazon RDS, in either the console or by using the equivalent commands in the AWS CLI\.
+ You must have already completed the steps in the tutorial [Tutorial: Rotating a Secret for an AWS Database](tutorials_db-rotate.md), without deleting the database and user as described in the final section\. That provides you with the following:
  + An Amazon RDS MySQL database called **MyTestDatabase** that's running in an instance called **MyTestDatabaseInstance**\.
  + A master user named **adminuser** that has administrative permissions\.
  + A secret named **MyTestDatabaseMasterSecret** that has the credentials for **adminuser** stored in it\.

## Step 1: Create a New User for Your Database and a User Secret<a name="tut-db-rotate-master-step1"></a>

From the original tutorial, you have an Amazon RDS MySQL database with a single admin *master* user\. You also have a secret that can be queried to retrieve the latest credentials for that master user\. In this step, you create a new, more\-limited user and store its credentials in a secret\. This secret could be used by, for example, a mobile app that needs to query information from the database\. The user doesn't need anything other than read permissions, so it can't change its own password\.

1. Open a command prompt where you can run the AWS CLI and your MySQL client \(as installed in the previous tutorial\)\. Run the following commands to retrieve the master secret\. Then use the secret to sign in to the MySQL server\.

   ```
   $ secret=$(aws secretsmanager get-secret-value --secret-id MyTestDatabaseMasterSecret | jq .SecretString | jq fromjson)
   $ user=$(echo $secret | jq -r .username)
   $ password=$(echo $secret | jq -r .password)
   $ endpoint=$(echo $secret | jq -r .host)
   $ port=$(echo $secret | jq -r .port)
   $ mysql -h $endpoint -u $user -P $port -p$password
   ```

1. Now run the following commands at the mysql> prompt to create a new, restricted\-permissions user\. Replace the sample password with one only you know, but remember it for the following steps\.

   ```
   mysql> CREATE USER mytestuser IDENTIFIED BY 'ReplaceThisWithASecurePassword';
   Query OK, 0 rows affected (0.07 sec)
   ```

   ```
   mysql> GRANT SELECT on *.* TO mytestuser;
   Query OK, 0 rows affected (0.08 sec)
   ```

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the page with the list of secrets in your account, choose **Store a new secret**\.

1. On the **Create Secret** page, in the **Select secret type** section, choose **Credentials for RDS database**\.

1. For **User name**, type **mytestuser** to match the name of the user that you created in Step 1b\.

1. For **Password**, type the same password that you provided for **mytestuser** in Step 1b\.

1. For **Select the encryption key**, leave it set to **DefaultEncryptionKey**\.

1. For **Select which RDS database this secret will access**, choose the instance **MyTestDatabaseInstance** that you created in the previous tutorial\.

1. Choose **Next**\.

1. In the **Secret name and description** section, for **Secret name** type **MyTestDatabaseUserSecret**\.

1. In the **Configure automatic rotation** section, leave rotation disabled for now\. Choose **Next**\.

1. In the **Review** section, verify your details and then choose **Store**\.

   You're returned to the list of secrets, which now includes your new secret\.

## Step 2: Validate Your Initial Secret<a name="tut-db-rotate-master-step2"></a>

Before you configure your secret to rotate automatically, you should verify that the information in your secret is correct and can be used to connect to the database\. In the previous tutorial, you installed a Linux\-based MySQL client component\. Continue to use that tool here\.

If you're running this tutorial on some other platform, you might have to translate these commands into equivalents for your platform\. At the very least, you can retrieve the secret by using either the AWS CLI or the Secrets Manager console\. Cut and paste the user name and password into whatever MySQL database client you have\.

1. Run commands that retrieve the secret and store them temporarily\.

   ```
   $ secret=$(aws secretsmanager get-secret-value --secret-id MyTestDatabaseUserSecret | jq .SecretString | jq fromjson)
   $ user=$(echo $secret | jq -r .username)
   $ password=$(echo $secret | jq -r .password)
   $ endpoint=$(echo $secret | jq -r .host)
   $ port=$(echo $secret | jq -r .port)
   ```

   The first command retrieves the secret from Secrets Manager\. Then it pulls the `SecretString` field out of the JSON response and stores it in an environment variable\. Each of the commands that follow parses out one additional credential piece or connection detail, and stores it in a separate environment variable\.

1. Run a command that uses the parsed details to access your database\.

   ```
   $ mysql -h $endpoint -u $user -P $port -p$password
   Warning: Using a password on the command line interface can be insecure.
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 44
   Server version: 5.6.39 MySQL Community Server (GPL)
   
   Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
   
   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.
   
   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
   
   mysql>
   ```

1. At the `mysql>` prompt, run a command that verifies your connection to the database by using the credentials from the secret\. Ensure that the **Current user** in the output is the user that's specified in your secret\.

   ```
   mysql> status;
   --------------
   /apollo/env/envImprovement/bin/mysql_real  Ver 14.14 Distrib 5.6.39, for Linux (x86_64) using  EditLine wrapper
   
   Connection id:          48
   Current database:
   Current user:           mytestuser@192-168-1-1.example.com
   SSL:                    Not in use
   Current pager:          less
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.39 MySQL Community Server (GPL)
   Protocol version:       10
   Connection:             mytestdatabaseinstance.randomcharacters.region.rds.amazonaws.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 2 hours 52 min 24 sec
   
   Threads: 2  Questions: 15249  Slow queries: 0  Opens: 319  Flush tables: 1  Open tables: 80  Queries per second avg: 1.474
   --------------
   ```

1. Close the connection with the following command:

   ```
   mysql> quit
   Bye
   ```

## Step 3: Configure Rotation for Your Secret<a name="tut-db-rotate-master-step3"></a>

Now that the initial credentials in your secret have been validated, you can configure and start your first rotation\.

1. In the Secrets Manager console, choose the secret **MyTestDatabaseUserSecret**\.

1. On the secret details page, in the **Rotation configuration** section, choose **Edit rotation**\.

1. On the **Edit rotation configuration** page, choose **Enable automatic rotation**\.

1. For **Select rotation interval**, choose **30 days**\.

1. Under **Select which secret will be used to perform the rotation**, choose **Use a secret that I have previously stored in AWS Secrets Manager**\.

1. In the list of secrets that appears, choose **MyTestDatabaseMasterSecret**\.

1. Choose **Save**\.

   Rotation configuration begins, but the initial rotation fails because of one step that you must \(for security purposes\) perform yourself\.

1. When rotation configuration completes, the following message appears at the top of the page:

   *Your secret test/MyTestDatabaseUserSecret has been successfully stored and secret rotation is enabled\. To finish configuring rotation, you need to provide the *role* permissions to access the value of the secret arn:aws:secretsmanager:us\-east\-1:*123456789012*:secret:MyTestDatabaseMasterSecret\-*QZhDKU**\.

   You need to manually modify the policy for the role to grant the rotation function GetSecretValue access to the master secret\. We can't do this for you for security reasons\. Rotation of the secret continues to fail until you complete the following steps because the rotation policy doesn't have access to your master secret\.

1. Copy the Amazon Resource Name \(ARN\) of the master secret from the message to your clipboard\.

1. Choose the link on the word "role" in the message\. This opens the role details page in the IAM console, for the role that's attached to the Lambda rotation function that Secrets Manager created for you\.

1. On the **Permissions** tab, choose **Add inline policy**, and then set the following values:
   + For **Service**, choose **Secrets Manager**\.
   + For **Actions**, choose **GetSecretValue**\.
   + For **Resources**, choose **Add ARN** next to the **secret** resource type entry\.
   + In the **Add ARN\(s\)** dialog box, paste the ARN of the master secret that you copied previously, and then choose **Add**\.

1. Choose **Review policy**, and for **Name**, type **AccessToMasterSecret**\.

1.  Choose **Create policy**\.
**Note**  
As an alternative to using the Visual Editor as described in the previous steps, you can paste the following statement into an existing or new policy:  

   ```
   { "Effect": "Allow", "Action": [ "secretsmanager:GetSecretValue" ], "Resource": "<ARN of the master secret>" }
   ```

1. Return to the AWS Secrets Manager console\.

1. On the **Secrets** list page, choose the name of your user secret\.

1. Choose **Retrieve secret value** and view your current password\. It's either the original password or a new one that's created by a successful rotation\. If the original password is still there, close the **Secret value** section and reopen it until it successfully changes\. It might take a few minutes\. After it does change, move on to the next step\.

## Step 4: Verify Successful Rotation<a name="tut-db-rotate-master-step4"></a>

Now that you have rotated the secret, you can confirm that the new credentials in the secret work to connect with your database\.

1. Run the commands that retrieve the secret, and store them temporarily in environment variables\.

   ```
   $ secret=$(aws secretsmanager get-secret-value --secret-id MyTestDatabaseUserSecret | jq .SecretString | jq fromjson)
   $ user=$(echo $secret | jq -r .username)
   $ password=$(echo $secret | jq -r .password)
   $ endpoint=$(echo $secret | jq -r .host)
   $ port=$(echo $secret | jq -r .port)
   ```

1. Using the same MySQL client that you installed previously, run the command that uses the parsed details to access your database\.

   ```
   $ mysql -h $endpoint -u $user -P $port -p$password
   Warning: Using a password on the command line interface can be insecure.
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 44
   Server version: 5.6.39 MySQL Community Server (GPL)
   
   Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
   
   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.
   
   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
   
   mysql>
   ```

1. At the `mysql>` prompt, run a command that verifies your connection to the database by using the credentials from the secret\. Check that the **Current user** in the output is either the user that's specified in your first user secret, or that same user name suffixed with "\_clone"\. This shows that the rotation has successfully cloned your original nuser and alternated it in a new version of the secret\.

   ```
   mysql> status;
   --------------
   /apollo/env/envImprovement/bin/mysql_real  Ver 14.14 Distrib 5.6.39, for Linux (x86_64) using  EditLine wrapper
   
   Connection id:          48
   Current database:
   Current user:           mytestuser_clone@192-168-1-1.example.com
   SSL:                    Not in use
   Current pager:          less
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.39 MySQL Community Server (GPL)
   Protocol version:       10
   Connection:             mytestdatabaseinstance.randomcharacters.region.rds.amazonaws.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 2 hours 52 min 24 sec
   
   Threads: 2  Questions: 15249  Slow queries: 0  Opens: 319  Flush tables: 1  Open tables: 80  Queries per second avg: 1.474
   --------------
   ```

1. Close the connection with the following command:

   ```
   mysql> quit
   Bye
   ```

## Step 5: Clean Up<a name="tut-db-rotate-master-step5"></a>

Because databases and secrets can incur charges on your AWS bill, you should remove the database instance and the secret that you created in this tutorial\.

**To delete the secret**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the list of secrets, choose the **MyTestDatabaseSecret** secret that you created for this tutorial\.

1. Choose **Actions**, and then choose **Delete secret**\.

1. In the **Schedule secret deletion** dialog box, for **Enter a waiting period**, type **7**\. This is the minimum value allowed\.

1. Choose **Schedule deletion**\.

   After the number of days in the recovery window elapses, Secrets Manager removes the secret permanently\.

**To delete the database instance**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.

1. In the list of available instances, choose the **MyTestDatabaseInstance** instance that you created for this tutorial\.

1. Choose **Instance actions**, and then choose **Delete**\.

1. On the **Delete DB Instance** page, in the **Options** section, for **Create final snapshot**, choose **No**\.

1. Select the acknowledgement that all of your data will be lost, and then choose **Delete**\.