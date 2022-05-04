# Tutorial: Set up single user rotation for AWS Secrets Manager<a name="tutorials_rotation-single"></a>

In this tutorial, you learn how to set up single user rotation for a secret that contains database admin credentials\. *Single user rotation* is a rotation strategy where Secrets Manager updates a single user's credentials in both the secret and the database\. For more information, see [Rotation strategies](rotating-secrets_strategies.md)\. 

A large part of this tutorial is setting up a realistic environment\. To show you how rotation works, this tutorial uses an example Amazon RDS MySQL database\. For security, the database is in a VPC that doesn't allow internet access\. To connect to the database from your local computer through the internet, you use a *bastion host*, a server in the VPC that can connect to the database, but that also allows SSH connections from the internet\. The bastion host in this tutorial is an Amazon EC2 instance, and the security groups for the instance prevent other types of connections\. 

As part of the prerequisites for the tutorial, you also create a secret that contains admin credentials for the database\. The secret doesn't initially have rotation turned on\. In this tutorial, you'll learn how to turn on automatic rotation\.

**Contents**
+ [Permissions](#tutorials_rotation-single_permissions)
+ [Prerequisites](#tutorials_rotation-single_step-setup)
  + [Prereq A: Amazon RDS database, Amazon VPC, and a Secrets Manager secret](#tutorials_rotation-single_step-setup1)
  + [Prereq B: Internet gateway](#tutorials_rotation-single_step-setup2)
  + [Prereq C: Security group](#tutorials_rotation-single_step-setup-sec-group)
  + [Prereq D: Amazon EC2 instance](#tutorials_rotation-single_step-setup_ec2)
  + [Prereq E: MySQL Workbench](#tutorials_rotation-single_step-setup3)
+ [Step 1: Connect with original password](#tutorials_rotation-single_step-connect)
+ [Step 2: Create a Secrets Manager endpoint](#tutorials_rotation-single_step-network)
+ [Step 3: Rotate the secret](#tutorials_rotation-single_step-rotate)
+ [Step 4: Test the rotated password](#tutorials_rotation-single_step-connect-again)
+ [Step 5: Clean up resources](#tutorials_rotation-single_step-cleanup)
+ [Next steps](#tutorials_rotation-single_step-next)

## Permissions<a name="tutorials_rotation-single_permissions"></a>

For the tutorial prerequisites, you need administrative permissions to your AWS account\. In a production setting, it is a best practice to use different roles for each of the steps\. For example, a role with database admin permissions would create the Amazon RDS database, and a role with network admin permissions would set up the VPC and security groups\. For the tutorial steps, we recommend you continue using the same identity\.

For information about how to set up permissions in a production environment, see [Authentication and access control for AWS Secrets Manager](auth-and-access.md)\.

## Prerequisites<a name="tutorials_rotation-single_step-setup"></a>

**Topics**
+ [Prereq A: Amazon RDS database, Amazon VPC, and a Secrets Manager secret](#tutorials_rotation-single_step-setup1)
+ [Prereq B: Internet gateway](#tutorials_rotation-single_step-setup2)
+ [Prereq C: Security group](#tutorials_rotation-single_step-setup-sec-group)
+ [Prereq D: Amazon EC2 instance](#tutorials_rotation-single_step-setup_ec2)
+ [Prereq E: MySQL Workbench](#tutorials_rotation-single_step-setup3)

### Prereq A: Amazon RDS database, Amazon VPC, and a Secrets Manager secret<a name="tutorials_rotation-single_step-setup1"></a>

Rather than creating these resources through the console, for this tutorial you use AWS CloudFormation with a provided template to create a CloudFormation stack\. For more information about CloudFormation and templates, see [AWS CloudFormation concepts](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-whatis-concepts.html)\.

**To get the CloudFormation template**
+ Go to [Create a Secrets Manager secret for an Amazon RDS MySQL DB instance with AWS CloudFormation](cfn-example_RDSsecret_no-rotation.md), and save the code to a new file\. You can use either JSON or YAML\.

**To create the stack from the template**

1. Open the AWS CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. Under **Stacks**, choose **Create stack** and then choose **With new resources**\.

1. On the **Create stack** page, for **Prepare template**, choose **Template is ready**\. 

1. For **Template source**, choose **Upload a template file**\.

1. Choose **Choose file**, and then choose the file you saved\.

1. Choose **Next**\.

1. On the **Specify stack details** page, name the stack **SecretsManagerRotationTutorial**, and then choose **Next**\.

1. On the **Configure stack options** page, choose **Next**\. 

1. On the Review page, choose **Create stack**\.

CloudFormation opens the new stack in the console and begins creating the resources in the template\. This process can take up to 30 minutes\. You can see how far along it is in the process under **Events**\. Choose the refresh button to update the events\.

When the stack is complete, under **Logical ID** you see **SecretsManagerRotationTutorial** with **Status CREATE\_COMPLETE**\.

### Prereq B: Internet gateway<a name="tutorials_rotation-single_step-setup2"></a>

For this tutorial, you need to create an internet gateway and attach it to the VPC to allow traffic to leave the VPC\. You create a route in the route table so that traffic destined for outside the VPC is sent to the internet gateway\. For more information, see [Connect subnets to the internet using an internet gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html)\.

**To create an internet gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Choose **Internet Gateways** and then choose **Create internet gateway**\.

1. On the **Create internet gateway** page, for **Name tag**, enter**SecretsManagerTutorialGateway**, and then choose **Create internet gateway**\.

1. On the **igw\-\*\*\*\* / SecretsManagerTutorialGateway** page, in the green banner, choose **Attach to a VPC**\.

1. On the **Attach to VPC** page, for **Available VPCs**, choose **vpc\-\*\*\*\* \- SecretsManagerTutorial**, and then choose **Attach internet gateway**\.

**To add a route for the internet gateway**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Choose **Route tables**, and then select the **Route table ID** associated with the **VPC** **vpc\-\*\*\*\* \| SecretsManagerTutorial**\. You might need to scroll to see the column **VPC** in the table\.

1. Choose **Actions** and then choose **Edit routes**\.

1. On the **Edit routes **page, choose **Add Route**, and then do the following:

   1. For **Destination**, enter **0\.0\.0\.0/0**\.

   1. For **Target**, choose **Internet Gateway** and then choose **igw\-\*\*\*\* \(SecretsManagerTutorialGateway\)**\.

   1. Choose **Save changes**\.

### Prereq C: Security group<a name="tutorials_rotation-single_step-setup-sec-group"></a>

Create a security group to allow inbound SSH traffic to access a Amazon EC2 bastion host you'll create in a later step\.

**To allow SSH access to the bastion host**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Choose **Security Groups** and then choose **Create Security Group**\.

1. For **Security group name**, enter **SecretsManagerTutorialAccess**\.

1. For **Description**, enter **Allows SSH access to bastion host**\.

1. For **VPC**, choose **vpc\-\*\*\*\* \(SecretsManagerTutorial\)**\. You might need to delete the prefilled text to see other choices\.

1. Under **Inbound Rules**, choose **Add Rule**\.

1. For Inbound rule 1, do the following:

   1. For **Type**, choose **SSH**\.

   1. For **Source**, choose **My IP**\. 

1. Choose **Create security group**\.

### Prereq D: Amazon EC2 instance<a name="tutorials_rotation-single_step-setup_ec2"></a>

To access the database in the VPN, you use a bastion host\. The bastion host is also in the VPN, but it allows your local computer to connect to it with SSH\. From the bastion host, you can access the database\.

**To create an EC2 instance for your bastion host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Instances** and then choose **Launch Instances**\.

1. On the **Step 1** page, choose the default **Amazon Linux 2 AMI \(HMV\) Kernel 5\.10** and then choose **Select**\.

1. On the **Step 2** page, choose the default **t2\.micro** and then choose **Next: Configure Instance Details**\.

1. On the Step 3 page, do the following:

   1. For **Network**, choose **vpc\-\*\*\*\* SecretsManagerTutorial**\.

   1. For **Auto\-assign Public IP**, choose **Enable**\.

   1. Choose **Next: Add Storage**\.

1. On the **Step 4** page, choose **Next: Add Tags**\.

1. On the **Step 5** page, choose Add Tag\. 

   For Key enter **Name**, and for **Value** enter **SecretsManagerTutorialInstance**, and then choose **Next: Configure Security Group**\.

1. On the **Step 6** page, for **Assign a security group**, choose **Select an existing security group**\.

1. For Security group ID, choose the security groups with the names **default** and **SecretsManagerTutorialAccess**, and then choose **Review and Launch**\.

1. On the **Step 7** page, choose **Launch**\.

1. In the **Select an existing key pair** dialog box, do the following:

   1. Select **Create a new key pair**\.

   1. For **Key pair name**, enter **SecretsManagerTutorialKeyPair**\.

   1. Choose **Download Key Pair**\. You will use this private key file to connect to the instance in a later step\.

   1. Choose **Launch Instances**\.

### Prereq E: MySQL Workbench<a name="tutorials_rotation-single_step-setup3"></a>

To connect to the database, you use a MySQL client tool\. In this tutorial, you use MySQL Workbench, a GUI\-based application\.

To install MySQL Workbench, see [Download MySQL Workbench](http://dev.mysql.com/downloads/workbench/)\.

To connect to the database, you first create a connection configuration in MySQL Workbench\. For the configuration, you need some information from both Amazon EC2 and Amazon RDS\.

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

## Step 1: Connect with original password<a name="tutorials_rotation-single_step-connect"></a>

The first step is to check that the information in the secret contains valid credentials for the database\.

**To retrieve the password from the secret**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose **Secrets**, and then choose the secret **SecretsManagerTutorialAdmin\-\*\*\*\***\.

1. On the **Secret details** page, scroll down and choose **Retrieve secret value**\.

1. In the **Key/value** table, copy the **Secret value** for **password**\.

**To test the credentials**

1. In MySQL Workbench, choose the connection **SecretsManagerTutorial**\.

1. In the **Open SSH Connection** dialog box, for **Password**, paste the password you retrieved from the secret, and then choose **OK**\.

   The first time you connect, you might see a warning dialog box about the server fingerprint\. Choose **OK** to continue\.

If the credentials are valid, then MySQL Workbench opens to the design page for the database\.

## Step 2: Create a Secrets Manager endpoint<a name="tutorials_rotation-single_step-network"></a>

The next step is to create a Secrets Manager endpoint within the VPC\. When you set up automatic rotation, Secrets Manager creates the Lambda rotation function within the VPC so that it has access to the database\. The Lambda rotation function also calls Secrets Manager\. By creating a Secrets Manager endpoint within the VPC, you ensure that calls from Lambda to Secrets Manager don't leave AWS infrastructure\. Instead, they are routed to the Secrets Manager endpoint within the VPC\. For more information, see [Network access for the rotation function](rotation-network-rqmts.md)\.

**To create a Secrets Manager endpoint within the VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Under **Endpoints**, choose **Create Endpoint**\.

1. Scroll down to **Services**, enter **secretsmanager** to filter the list, and then select the Secrets Manager endpoint in your AWS Region\. For example, in the US East \(N\. Virginia\), choose `com.amazonaws.us-east-1.secretsmanager`\. 

1. For **VPC**, choose **vpc\*\*\*\* \(SecretsManagerTutorial\)**\.

1. For **Subnets**, select all **Availability Zones**, and then for each one, choose a **Subnet ID** to include\.

1. For **Security groups**, choose the default security group\.

1. For **Policy**, choose **Full access**\. 

1. Choose **Create endpoint**\.

## Step 3: Rotate the secret<a name="tutorials_rotation-single_step-rotate"></a>

Now you are ready to turn on rotation\. You start an immediate rotation, so Secrets Manager rotates the secret immediately when you save the secret\. You also turn on automatic rotation, so the secret is rotated every 10 days between midnight and 2:00 AM UTC\. 

**To turn on automatic rotation**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Open the secret **SecretsManagerTutorialAdmin\-*a1b2c3d4e5f6***, scroll down to **Rotation configuration**, and choose **Edit rotation**\.

1. In the **Edit rotation configuration** dialog, turn on **Automatic rotation**\.

1. For **Rotation schedule**, set a schedule of **Days**: **10** Days with **Duration**: **2h**\. Keep **Rotate immediately** selected\. 

1. For **Rotation function**, do the following:

   1. Choose **Create a rotation function**, and then for **Lambda rotation function**, enter **tutorial\-single\-user\-rotation**\.

   1. Secrets Manager will create a Lambda rotation function named **SecretsManagertutorial\-single\-user\-rotation**\.

   1. For **Use separate credentials**, choose **No**\.

1. Choose **Save**\.

   Secrets Manager returns to the the secret details page\. Secrets Manager uses CloudFormation to create resources such as the Lambda rotation function and an execution role that runs the Lambda function\. At the top of the secret details page, you can see the status of the CloudFormation resources\. When CloudFormation finishes deploying the resources, the banner changes to **Secret scheduled for rotation**\. Now Secrets Manager begins the first rotation\.

## Step 4: Test the rotated password<a name="tutorials_rotation-single_step-connect-again"></a>

After the first secret rotation, which might take a few seconds, you can check that the secret still contains valid credentials\. The password in the secret has changed from the original credentials\.

**To retrieve the new password from the secret**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose **Secrets**, and then choose the secret **SecretsManagerTutorialAdmin\-\*\*\*\***\.

1. On the **Secret details** page, scroll down and choose **Retrieve secret value**\.

1. In the **Key/value** table, copy the **Secret value** for **password**\.

**To test the credentials**

1. In MySQL Workbench, choose the connection **SecretsManagerTutorial**\.

1. In the **Open SSH Connection** dialog box, for **Password**, paste the password you retrieved from the secret, and then choose **OK**\.

   If the credentials are valid, then MySQL Workbench opens to the design page for the database\.

This shows that the secret rotation is successful\. The updated password in the secret is a valid password to connect to the database\. You have finished setting up automatic rotation, and the next rotation will happen in 10 days\.

## Step 5: Clean up resources<a name="tutorials_rotation-single_step-cleanup"></a>

If you want to try another rotation strategy, *alternating users rotation*, skip cleaning up resources and go to [Tutorial: Set up alternating users rotation for AWS Secrets Manager](tutorials_rotation-alternating.md)\. 

Otherwise, to avoid potential charges, and to remove the EC2 instance that has access to the internet, delete the following resources you created in this tutorial:
+ Amazon EC2 instance\. For instructions, see [Terminate an instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html#terminating-instances-console)\.
+ Secrets Manager endpoint\. For instructions, see [Delete a VPC endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/delete-vpc-endpoint.html)\.
+ Internet gateway\. First [Detach an internet gateway from your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#detach-igw), then [Delete an internet gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html#delete-igw)\.
+ AWS CloudFormation stack\. For instructions, see [Delete a stack](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-delete-stack.html)\.

## Next steps<a name="tutorials_rotation-single_step-next"></a>
+ Learn how to retrieve secrets in your applications\. See [Retrieve secrets from AWS Secrets Manager](retrieving-secrets.md)\.
+ Learn how to create a secret with automatic rotation using AWS CloudFormation, see [ AWS::SecretsManager::RotationSchedule](https://docs.aws.amazon.com/https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-rotationschedule.html) in the AWS CloudFormation User Guide\.
+ Learn about other rotation schedules\. See [Schedule expressions in Secrets Manager rotation](rotate-secrets_schedule.md)\.