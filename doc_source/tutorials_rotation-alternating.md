# Set up alternating users rotation for AWS Secrets Manager<a name="tutorials_rotation-alternating"></a>

In this tutorial, you learn how to set up alternating users rotation for a secret that contains database credentials\. *Alternating users rotation* is a rotation strategy where Secrets Manager clones the user and then alternates which user's credentials are updated\. This strategy is a good choice if you need high availability for your secret, because one of the alternating users has current credentials to the database while the other one is being updated\. For more information, see [Rotation strategy: alternating users](getting-started.md#rotating-secrets-two-users)\. 

To set up alternating users rotation, you need two secrets:
+ One secret with the credentials that you want to rotate\.
+ A second secret that has admin credentials\. 

  This user has permissions to clone the first user and change the first users's password\. In this tutorial, you have Amazon RDS create this secret for an admin user\. Amazon RDS also manages the admin password rotation\. For more information, see [Managed rotation for AWS Secrets Manager secrets](rotate-secrets_managed.md)\.

The first part of this tutorial is setting up a realistic environment\. To show you how rotation works, this tutorial uses an example Amazon RDS MySQL database\. For security, the database is in a VPC that restricts inbound internet access\. To connect to the database from your local computer through the internet, you use a *bastion host*, a server in the VPC that can connect to the database, but that also allows SSH connections from the internet\. The bastion host in this tutorial is an Amazon EC2 instance, and the security groups for the instance prevent other types of connections\. 

After you finish the tutorial, we recommend that you clean up the resources from the tutorial\. Don't use them in a production setting\.

Secrets Manager rotation uses an AWS Lambda function to update the secret and the database\. For information about the costs of using a Lambda function, see [Pricing](intro.md#asm_pricing)\.

**Topics**
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

**Topics**
+ [Prereq A: Amazon VPC](#tutorials_rotation-alternating-step-vpc)
+ [Prereq B: Amazon EC2 instance](#tutorials_rotation-alternating-step-setup_ec2)
+ [Prereq C: Amazon RDS database and a Secrets Manager secret for the admin credentials](#tutorials_rotation-alternating-step-database)
+ [Prereq D: Allow your local computer to connect to the EC2 instance](#tutorials_rotation-alternating-step-ec2connect)

### Prereq A: Amazon VPC<a name="tutorials_rotation-alternating-step-vpc"></a>

In this step, you create a VPC that you can launch an Amazon RDS database and an Amazon EC2 instance into\. In a later step, you'll use your computer to connect through the internet to the bastion and then to the database, so you need to allow traffic out of the VPC\. To do this, Amazon VPC attaches an internet gateway to the VPC and adds a route in the route table so that traffic destined for outside the VPC is sent to the internet gateway\.

Within the VPC, you create a Secrets Manager endpoint and an Amazon RDS endpoint\. When you set up automatic rotation in a later step, Secrets Manager creates a Lambda rotation function within the VPC so that it can access the database\. The Lambda rotation function also calls Secrets Manager to update the secret, and it calls Amazon RDS to get the database connection information\. By creating endpoints within the VPC, you ensure that calls from the Lambda function to Secrets Manager and Amazon RDS don't leave AWS infrastructure\. Instead, they are routed to the endpoints within the VPC\.

**To create a VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Choose **Create VPC**\.

1. On the **Create VPC** page, choose **VPC and more**\.

1. Under **Name tag auto\-generation**, under **Auto\-generate**, enter **SecretsManagerTutorial**\.

1. For **DNS options**, choose both **Enable DNS hostnames** and **Enable DNS resolution**\.

1. Choose **Create VPC**\.

**To create a Secrets Manager endpoint within the VPC**

1. In the Amazon VPC console, under **Endpoints**, choose **Create Endpoint**\.

1. Under **Endpoint settings**, for **Name**, enter **SecretsManagerTutorialEndpoint**\.

1. Under **Services**, enter **secretsmanager** to filter the list, and then select the Secrets Manager endpoint in your AWS Region\. For example, in the US East \(N\. Virginia\), choose `com.amazonaws.us-east-1.secretsmanager`\. 

1. For **VPC**, choose **vpc\*\*\*\* \(SecretsManagerTutorial\)**\.

1. For **Subnets**, select all **Availability Zones**, and then for each one, choose a **Subnet ID** to include\.

1. For **IP address type**, choose **IPv4**\.

1. For **Security groups**, choose the default security group\.

1. For **Policy**, choose **Full access**\. 

1. Choose **Create endpoint**\.

**To create an Amazon RDS endpoint within the VPC**

1. In the Amazon VPC console, under **Endpoints**, choose **Create Endpoint**\.

1. Under **Endpoint settings**, for **Name**, enter **RDSTutorialEndpoint**\.

1. Under **Services**, enter **rds** to filter the list, and then select the Amazon RDS endpoint in your AWS Region\. For example, in the US East \(N\. Virginia\), choose `com.amazonaws.us-east-1.rds`\. 

1. For **VPC**, choose **vpc\*\*\*\* \(SecretsManagerTutorial\)**\.

1. For **Subnets**, select all **Availability Zones**, and then for each one, choose a **Subnet ID** to include\.

1. For **IP address type**, choose **IPv4**\.

1. For **Security groups**, choose the default security group\.

1. For **Policy**, choose **Full access**\. 

1. Choose **Create endpoint**\.

### Prereq B: Amazon EC2 instance<a name="tutorials_rotation-alternating-step-setup_ec2"></a>

The Amazon RDS database you create in a later step will be in the VPC, so to access it, you need a bastion host\. The bastion host is also in the VPC, but in a later step, you configure a security group to allow your local computer to connect to the bastion host with SSH\. 

**To create an EC2 instance for a bastion host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Instances** and then choose **Launch Instances**\.

1. Under **Name and tags**, for **Name**, enter **SecretsManagerTutorialInstance**\.

1. Under **Application and OS Images**, keep the default **Amazon Linux 2 AMI \(HMV\) Kernel 5\.10**\.

1. Under **Instance type**, keep the default **t2\.micro**\.

1. Under **Key pair**, choose **Create key pair**\.

   In the **Create key pair** dialog box, for **Key pair name**, enter **SecretsManagerTutorialKeyPair**, and then choose **Create key pair**\.

   The key pair is automatically downloaded\.

1. Under **Network settings**, choose **Edit**, and then do the following:

   1. For **VPC**, choose **vpc\-\*\*\*\* SecretsManagerTutorial**\.

   1. For **Auto\-assign Public IP**, choose **Enable**\.

   1. For **Firewall**, choose **Select existing security group**\.

   1. For **Common security groups**, choose **default**\. 

1. Choose **Launch instance**\.

### Prereq C: Amazon RDS database and a Secrets Manager secret for the admin credentials<a name="tutorials_rotation-alternating-step-database"></a>

In this step, you create an Amazon RDS MySQL database and configure it so that Amazon RDS creates a secret to contain the admin credentials\. Then Amazon RDS automatically manages rotation of the admin secret for you\. For more information, see [Managed rotation](rotate-secrets_managed.md)\.

As part of creating your database, you specify the bastion host you created in the previous step\. Then Amazon RDS sets up security groups so that the database and the instance can access each other\. You add a rule to the security group attached to the instance to allow your local computer to connect to it as well\. 

**To create an Amazon RDS database with an Secrets Manager secret that contains the admin credentials**

1. In the Amazon RDS console, choose **Create database**\.

1. In the **Engine options** section, for **Engine type**, choose **MySQL**\.

1. In the **Templates** section, choose **Free tier**\.

1. In the **Settings** section, do the following:

   1. For **DB instance identifier**, enter **SecretsManagerTutorial**\.

   1. Under **Credential settings**, select **Manage master credentials in AWS Secrets Manager**\.

1. In the **Connectivity** section, for **Computer resource**, choose **Connect to an EC2 computer resource**, and then for **EC2 Instance**, choose **SecretsManagerTutorialInstance**\.

1. Choose **Create database**\.

### Prereq D: Allow your local computer to connect to the EC2 instance<a name="tutorials_rotation-alternating-step-ec2connect"></a>

In this step, you configure the EC2 instance you created in Prereq B to allow your local computer to connect to it\. To do this, you edit the security group that Amazon RDS added in Prereq C to include a rule that allows your computer's IP address to connect with SSH\. The rule allows your local computer \(identified by your current IP address\) to connect to the bastion host by using SSH over the internet\.

**To allow your local computer to connect to the EC2 instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the EC2 instance **SecretsManagerTutorialInstance**, on the **Security** tab, under **Security groups**, choose **sg\-\*\*\* \(ec2\-rds\-X\)**\.

1. Under **Input rules**, choose **Edit inbound rules**\.

1. Choose **Add rule**, and then for the rule, do the following:

   1. For **Type**, choose **SSH**\.

   1. For **Source type**, choose **My IP**\.

## Step 1: Create an Amazon RDS database user<a name="tutorials_rotation-alternating-step-database"></a>

First, you need a user whose credentials will be stored in the secret\. To create the user, log into the Amazon RDS database with admin credentials\. For simplicity, in the tutorial, you create a user with full permission to a database\. In a production setting, this is not typical, and we recommend that you follow the principle of least privilege\.

To connect to the database, you use a MySQL client tool\. In this tutorial, you use MySQL Workbench, a GUI\-based application\. To install MySQL Workbench, see [Download MySQL Workbench](http://dev.mysql.com/downloads/workbench/)\.

To connect to the database, create a connection configuration in MySQL Workbench\. For the configuration, you need some information from both Amazon EC2 and Amazon RDS\.

**To create a database connection in MySQL Workbench**

1. In MySQL Workbench, next to **MySQL Connections**, choose the \(\+\) button\.

1. In the **Setup New Connection** dialog box, do the following:

   1. For **Connection Name**, enter **SecretsManagerTutorial**\.

   1. For **Connection Method**, choose **Standard TCP/IP over SSH**\.

   1. On the **Parameters** tab, do the following:

      1. For **SSH Hostname**, enter the public IP address of the Amazon EC2 instance\.

         You can find the IP address on the Amazon EC2 console by choosing the instance **SecretsManagerTutorialInstance**\. Copy the IP address under **Public IPv4 DNS**\.

      1. For **SSH Username**, enter **ec2\-user**\.

      1. For **SSH Keyfile**, choose the key pair file **SecretsManagerTutorialKeyPair\.pem** you downloaded in the previous prerequisite\. 

      1. For **MySQL Hostname**, enter the Amazon RDS endpoint address\.

         You can find the endpoint address on the Amazon RDS console by choosing the database instance **secretsmanagertutorialdb**\. Copy the address under **Endpoint**\.

      1. For **Username**, enter **admin**\.

   1. Choose **OK**\.

**To retrieve the admin password**

1. In the Amazon RDS console, navigate to your database\.

1. On the **Configuration** tab, under **Master Credentials ARN**, choose **Manage in Secrets Manager**\.

   The Secrets Manager console opens\.

1. In the secret details page, choose **Retrieve secret value**\.

1. The password appears in the **Secret value** section\.

**To create a database user**

1. In MySQL Workbench, choose the connection **SecretsManagerTutorial**\. 

1. Enter the admin password you retrieved from the secret\. 

1.  In MySQL Workbench, in the **Query** window, enter the following commands \(including a strong password\) and then choose **Execute**\.

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

1. On the **Choose secret type** page, do the following:

   1. For **Secret type**, choose **Credentials for Amazon RDS database**\.

   1. For **Credentials**, enter the username **appuser** and the password you entered for the database user you created using MySQL Workbench\.

   1. For **Database**, choose **secretsmanagertutorialdb**\.

   1. Choose **Next**\.

1. On the **Configure secret** page, for **Secret name**, enter **SecretsManagerTutorialAppuser** and then choose **Next**\.

1. On the **Configure rotation** page, do the following:

   1. Turn on **Automatic rotation**\.

   1. For **Rotation schedule**, set a schedule of **Days**: **2** Days with **Duration**: **2h**\. Keep **Rotate immediately** selected\. 

   1. For **Rotation function**, choose **Create a rotation function**, and then for the function name, enter **tutorial\-alternating\-users\-rotation**\.

   1. For **Use separate credentials**, choose **Yes**, and then under **Secrets**, choose the secret named **rds\!cluster\.\.\.** which has a **Description** that includes the name of the database you created in this tutorial **secretsmanagertutorial**, for example `Secret associated with primary RDS DB instance: arn:aws:rds:Region:AccountId:db:secretsmanagertutorial`\.

   1. Choose **Next**\.

1. On the **Review** page, choose **Store**\.

   Secrets Manager returns to the the secret details page\. At the top of the page, you can see the rotation configuration status\. Secrets Manager uses CloudFormation to create resources such as the Lambda rotation function and an execution role that runs the Lambda function\. When CloudFormation finishes, the banner changes to **Secret scheduled for rotation**\. The first rotation is complete\.

## Step 3: Test the rotated secret<a name="tutorials_rotation-alternating_step-test-secret"></a>

Now that the secret is rotated, you can check that the secret contains valid new credentials\. The password in the secret has changed from the original credentials\.

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

If you want to try another rotation strategy, *single user rotation*, skip cleaning up resources and go to [Set up single user rotation for AWS Secrets Manager](tutorials_rotation-single.md)\. 

Otherwise, to avoid potential charges, and to remove the EC2 instance that has access to the internet, delete the following resources you created in this tutorial and its prerequisites:
+ Amazon RDS database instance\. For instructions, see [Deleting a DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_DeleteInstance.html) in the *Amazon RDS User Guide*\.
+ Amazon EC2 instance\. For instructions, see [Terminate an instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html#terminating-instances-console) in the *Amazon EC2 User Guide for Linux Instances*\.
+ Secrets Manager secret `SecretsManagerTutorialAppuser`\. For instructions, see [Delete an AWS Secrets Manager secret](manage_delete-secret.md)\.
+ Secrets Manager endpoint\. For instructions, see [Delete a VPC endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/delete-vpc-endpoint.html) in the *AWS PrivateLink Guide*\.
+ VPC endpoint\. For instructions, see [Delete your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#VPC_Deleting) in the *AWS PrivateLink Guide*\.

## Next steps<a name="tutorials_rotation-alternating_step-next"></a>
+ Learn how to [retrieve secrets in your applications](retrieving-secrets.md)\.
+ Learn about [other rotation schedules](rotate-secrets_schedule.md)\.