# Tutorial: Rotating a Secret for an AWS Database<a name="tutorials_db-rotate"></a>

In this tutorial, you create a secret for an AWS database and configure the database to rotate on a schedule\. You trigger one rotation manually, and then confirm the new version of the secret continues to provide access\.

**[Step 1: Set Up a Test Database](#tut-db-rotate-step1)**  
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

This tutorial assumes you have access to an AWS account and also assumes you can sign in to AWS as a user with full permissions to configure AWS Secrets Manager and Amazon RDS, either using the console or by using the equivalent commands in the AWS CLI\.

The tutorial uses a MySQL client tool to interact with the database and configure users, and to check status\. The tutorial includes the installation instructions at the appropriate point in the following steps \.

The tutorial also uses a Linux JSON parsing tool called **jq**\. To download the tool, see [jq on the GitHub website](https://stedolan.github.io/jq/)\.

**Important**  
For the sake of simplicity, this tutorial uses **jq** to parse the secret value into environment variables to allow for easy command line manipulation\. This is NOT a security best practice for a production environment\. In a production environment, we recommend that you do not store passwords in environment variables\.  
Also, the database configured in this tutorial allows access to the public Internet on port 3306, again for simplicity in setup for the tutorial\. To complete this tutorial, you must be able to access the MySQL database from your Internet\-connected computer by using the MySQL client tool\. To configure production servers securely, we recommend you follow the guidance in the Lambda and Amazon EC2 VPC documentation\.

**Important**  
For rotation to work, your network environment must permit the Lambda rotation function to communicate with your database and the Secrets Manager service\. Because this tutorial configures your database with public Internet access, Lambda automatically configures your rotation function to access the database through the public IP address\. If you instead block public Internet access to your database instance, then you must configure the Lambda function to run in the same VPC as the database instance\. Then you must either [configure your VPC with a private Secrets Manager endpoint](rotation-network-rqmts.md), or [configure the VPC with public Internet access by using a NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html), so that the Lambda rotation function can access the public Secrets Manager endpoint\.

## Required Permissions<a name="tut_db-rotate-perms"></a>

To successfully run this tutorial, you must have all of the permissions associated with the [SecretsManagerReadWrite AWS managed policy](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/SecretsManagerReadWrite)\. You must also have permission to create an IAM role and attach a permission policy to the role\. You can grant either the [IAMFullAccess AWS managed policy](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/IAMFullAccess), or explicitly assign `iam:CreateRole` and `iam:AttachRolePolicy`\. 

**Warning**  
The `iam:CreateRole` and `iam:AttachRolePolicy` enable a user to grant themselves any permission, so grant these policies only to trusted users in an account\.

## Step 1: Set Up a Test Database<a name="tut-db-rotate-step1"></a>

In this step, sign in to your account and set up a MySQL database in Amazon RDS\.

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. If the console displays the **Amazon Relational Database Service** welcome page, choose **Get started now**\.

   If the console displays your dashboard, choose **Instances**, and then choose **Launch DB instance**\.

1. On the **Select engine** page, choose **MySQL**, select **Only enable options eligible for RDS Free Usage Tier**, and then choose **Next**\.

1. On the **Specify DB details** page, in the **Instance specifications** section, leave all of the settings at the default values\.

1. In the **Settings** section, make the following choices:
   + **DB instance identifier**: **MyTestDatabaseInstance**
   + **Master username**: **adminuser**\.
   + **Master password**: Type a secure initial password, and repeat the password in the **Confirm password** box\. *Be sure to remember this password\.* You need it when you create your secret in Step 2\.

1. Choose **Next**\.

1. On the **Configure advanced settings** page, in the **Network & Security** section, set **Public accessibility** to **Yes**\. This setting automatically adds a public Elastic IP address to the database instance\. Leave all other network settings at the default values\.
**Note**  
To configure the tutorial correctly, use these settings at a minimum\. If you require a private VPC, then the Lambda function must be configured to run in that VPC\. Next, you must either [configure your VPC with a private Secrets Manager endpoint](rotation-network-rqmts.md) or [configure the VPC with public Internet access by using a NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) so that the Lambda rotation function can access the public Secrets Manager endpoint\.

1. In the **Database options** section, for **Database name**, type **MyTestDatabase**\. Leave the other settings at default values\.

1. In the **Backup** section, set the **Backup retention** period to **0 days** to disable backups\. 

1. Leave the settings in all other sections at the default values\.

1. Choose **Launch DB instance**\.

1. Choose **View DB instance summary**, and wait until the instance becomes active before proceeding with the next step\. This step can take several minutes to complete\.

1. When the **Summary** section **DB instance status** shows **Available**, refresh the page, and then scroll down to the **Connect** section\.

1. In the **Connect** section, choose the name of the security group with the type **CIDR/IP \- Inbound**\. This opens the Amazon EC2 console for that security group\.

1. At the bottom of the page, choose the **Inbound** tab, and then choose **Edit**\.

   One existing rule enables access to the database from the current VPC\. You need to enable access to the database from the public Internet so that your MySQL client tool and your hypothetical customers running applications can all access this database\. 
**Warning**  
The following step allows Internet access your test database \. Secrets Manager uses this configuration for this tutorial\. For a secure setup, block public access to your database instance\. Then, configure the Lambda function to run in the same VPC as your database instance\. You must then either [configure your VPC with a Secrets Manager endpoint](rotation-network-rqmts.md), or [configure the VPC with public internet access by using a NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)\. This enables the Lambda rotation function to access a Secrets Manager endpoint\. When you've completed the tutorials on this database, we recommend you remove the rule you create in the next step\.

1. Under **Source**, change it from **Custom** to **Anywhere**\. Leave the Port Range at **3306**, the default port for MySQL\.

1. Choose **Save**\.

## Step 2: Create Your Secret<a name="tut-db-rotate-step2"></a>

In this step, you create a secret in Secrets Manager, and populate the secret with the details of your test database and the credentials of your master user\. 

**To create your secret**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Ensure you set your console to the same region as you created the Amazon RDS MySQL database in the previous step\.

1. Choose **Store a new secret**\.

1. On the **Create Secret** page, in the **Select secret type** section, choose **Credentials for RDS database**\.

1. For **User name**, type **adminuser** to match the name of the master user you previously provided in Step 1\.5\.

1. For **Password**, type the same password you provided for **adminuser** in Step 1\.5\.

1. For **Select the encryption key**, leave it set to **DefaultEncryptionKey**\. If you use a custom master key \(CMK\) instead of the default for the account, then you can be charged for the use of the custom CMK\.

1. For **Select which RDS database this secret will access**, choose the instance **MyTestDatabaseInstance** you created in Step 1\.

1. Choose **Next**\.

1. In the **Secret name and description** section, for **Secret name**, type **MyTestDatabaseMasterSecret**\.

1. In the **Configure automatic rotation** section, disable rotation for now\. Choose **Next**\.

1. In the **Review** section, verify your details, and then choose **Store**\.

   You return to the list of secrets, which now includes your new secret\.

## Step 3: Validate Your Initial Secret<a name="tut-db-rotate-step3"></a>

Before you configure your secret to rotate automatically, you should verify you have the correct information in your secret and can be used to connect to the database\. In this tutorial, we describe how to install a Linux\-based MySQL client component\. Then run a Linux command to retrieve the secret and connect to the database using the credentials configured in the secret\.

If you run this tutorial on another platform, you might have to translate these commands into equivalents for your platform\. At the very least, you can retrieve the secret by using either the AWS CLI or the Secrets Manager console\. Then cut and paste the user name and password into the MySQL database client\.

1. Install a MySQL client\. For example:

   ```
   $ sudo yum install mysql
   ...
   Installed:
     mysql.noarch 0:5.5-1.3.amzn1
   
   Dependency Installed:
     mysql55.x86_64 0:5.5.24-1.24.amzn1                                      mysql55-common.x86_64 0:5.5.24-1.24.amzn1
   
   Complete!
   $
   ```

1. Run commands to retrieve the secret and store them temporarily\.

   ```
   $ secret=$(aws secretsmanager get-secret-value --secret-id MyTestDatabaseMasterSecret | jq .SecretString | jq fromjson)
   $ user=$(echo $secret | jq -r .username)
   $ password=$(echo $secret | jq -r .password)
   $ endpoint=$(echo $secret | jq -r .host)
   $ port=$(echo $secret | jq -r .port)
   ```

   The first command retrieves the secret from Secrets Manager\. Then the command retrieves the `SecretString` field out of the JSON response and stores it in an environment variable\. Each of the following commands parses out one additional credential piece or connection detail, and stores it in a separate environment variable\.

1. Run a command using the parsed details to access your database\.

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
**Troubleshooting tip:**  
If the `mysql` command fails to connect to the database, then you should check the security group attached to the VPC with the database \. The default rules in a security group enable all outbound traffic, but the rules block all inbound traffic except for the traffic you explicitly allow by defining a rule\. If you run your computer on the public Internet then your security group must enable inbound traffic from the Internet to the TCP port you configured your database communication, typically port 3306\. If you configure MySQL to use a different TCP port, ensure you update the security rule to match\. 

1. At the `mysql>` prompt, run a command to verify your connection to the database by using the credentials from the secret\. Ensure that the **Current user** in the output contains the user specified in your secret\.

   ```
   mysql> status;
   --------------
   /path/mysql_real  Ver 14.14 Distrib 5.6.39, for Linux (x86_64) using  EditLine wrapper
   
   Connection id:          48
   Current database:
   Current user:           adminuser@192-168-1-1.example.com
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

## Step 4: Configure Rotation for Your Secret<a name="tut-db-rotate-step4"></a>

After you validate the initial credentials in your secret , you can configure and start your first rotation\.

1. In the Secrets Manager console, choose the secret **MyTestDatabaseMasterSecret**\.

1. On the **Secret details** page, in the **Rotation configuration** section, choose **Edit rotation**\.

1. On the **Edit rotation configuration** page, choose **Enable automatic rotation**\.

1. For **Select rotation interval**, choose **30 days**\.

1. Under **Select which secret will be used to perform the rotation**, choose **Use this secret**\.

1. Choose **Save**\. Secrets Manager begins to configure rotation for your secret, including creating the Lambda rotation function and attaching a role that enables Secrets Manager to invoke the function\. 

1. Stay on the console page with the **Rotation is being configured** message, until the message changes to **Your secret MyTestDatabaseMasterSecret has been successfully stored and secret rotation is enabled\.**

## Step 5: Verify Successful Rotation<a name="tut-db-rotate-step5"></a>

After you rotate the secret, you can confirm the new credentials in the secret work to connect with your database\.

1. Run the commands to retrieve the secret and store them temporarily in environment variables\.

   ```
   $ secret=$(aws secretsmanager get-secret-value --secret-id MyTestDatabaseMasterSecret | jq .SecretString | jq fromjson)
   $ user=$(echo $secret | jq -r .username)
   $ password=$(echo $secret | jq -r .password)
   $ endpoint=$(echo $secret | jq -r .host)
   $ port=$(echo $secret | jq -r .port)
   ```

1. Using the same MySQL client you installed previously, run the command using the parsed details to access your database\.

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

1. At the `mysql>` prompt, run a command verifying your connection to the database by using the credentials from the secret\. 

   ```
   mysql> status;
   --------------
   /path/mysql_real  Ver 14.14 Distrib 5.6.39, for Linux (x86_64) using  EditLine wrapper
   
   Connection id:          48
   Current database:
   Current user:           adminuser@192-168-1-1.example.com
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

1. Ensure that the **Current user** in the output matches the user specified in your secret\.

1. Close the connection with the following command:

   ```
   mysql> quit
   Bye
   ```

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

1. Select the acknowledgement all of your data will be lost, and then choose **Delete**\.