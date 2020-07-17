# AWS Secrets Manager Best Practices<a name="best-practices"></a>

The following recommendations help you to more securely use AWS Secrets Manager:

**Topics**
+ [Protecting Additional Sensitive Information](#best-practice_what-not-to-put-in-secret-text)
+ [Improving Performance by Using the AWS provided Client\-side Caching Components](#best-practice_client-caching-components)
+ [Mitigating the Risks of Logging and Debugging Your Lambda Function](#best-practice_lamda-debug-statements)
+ [Mitigating the Risks of Using the AWS CLI to Store Your Secrets](#best-practice_cli-exposure-risks)
+ [Cross\-Account Access – Should I Specify a User/Role or the Account?](#best-practice_cross-account-role-vs-account)
+ [Running Everything in a VPC](#best-practice_using-a-vpc)
+ [Tag Your Secrets](#best-practice_tagging)
+ [Rotating Secrets on a Schedule](#best-practice-rotating-secrets)
+ [Auditing Access to Secrets](#w71aac11c23)
+ [Monitoring Your Secrets with AWS Config](#aws-config)

## Protecting Additional Sensitive Information<a name="best-practice_what-not-to-put-in-secret-text"></a>

A secret often includes several pieces of information besides the user name and password\. Depending on the database, service, or website, you can choose to include additional sensitive data\. This data can include password hints, or question\-and\-answer pairs you can use to recover your password\.

Ensure you protect any information that might be used to gain access to the credentials in the secret as securely as the credentials themselves\. Don't store this type of information in the `Description` or any other non\-encrypted part of the secret\.

Instead, store all such sensitive information as part of the encrypted secret value, either in the `SecretString` or `SecretBinary` field\. You can store up to  65536 bytes in the secret\. In the `SecretString` field, the text usually takes the form of JSON key\-value string pairs, as shown in the following example:

```
{
  "engine": "mysql",
  "username": "user1",
  "password": "i29wwX!%9wFV",
  "host": "my-database-endpoint.us-east-1.rds.amazonaws.com",
  "dbname": "myDatabase",
  "port": "3306"
}
```

## Improving Performance by Using the AWS provided Client\-side Caching Components<a name="best-practice_client-caching-components"></a>

To use your secrets most efficiently, you should not simply retrieve the secret value from Secrets Manager every time you need to use the credentials\. Instead, use a supported Secrets Manager client component to cache your secrets and update them only when required because of rotation\. AWS has created such client components for you and made them available as open\-source\. For more information, see [Using the AWS\-developed Open Source Client\-side Caching Components](use-client-side-caching.md#use-client-side-caching-components)

## Mitigating the Risks of Logging and Debugging Your Lambda Function<a name="best-practice_lamda-debug-statements"></a>

When you create a custom Lambda rotation function to support your Secrets Manager secret, be careful about including debugging or logging statements in your function\. These statements can cause information in your function to be written to CloudWatch\. Ensure the logging of information to CloudWatch doesn't include any of the sensitive data stored in the encrypted secret value\. Also, if you do choose to include any such statements in your code during development for testing and debugging purposes, make sure you remove those lines from the code before using the code in production\. Also, remember to remove any logs including sensitive information collected during development after you no longer need it\.

The Lambda functions provided by AWS and [supported databases](intro.md#full-rotation-support) don't include logging and debug statements\. 

## Mitigating the Risks of Using the AWS CLI to Store Your Secrets<a name="best-practice_cli-exposure-risks"></a>

When you use the AWS Command Line Interface \(AWS CLI\) to invoke AWS operations, you enter those commands in a command shell\. For example, you can use the Windows command prompt or Windows PowerShell, or the Bash or Z shell, among others\. Many of these command shells include functionality designed to increase productivity\. But this functionality can be used to compromise your secrets\. For example, in most shells, you can use the up arrow key to see the last entered command\. The *command history* feature can be exploited by anyone who accesses your unsecured session\. Also, other utilities that work in the background might have access to your command parameters, with the intended goal of helping you perform your tasks more efficiently\. To mitigate such risks, ensure you take the following steps:
+ Always lock your computer when you walk away from your console\.
+ Uninstall or disable console utilities you don't need or no longer use\.
+ Ensure the shell or the remote access program, if you are using one or the other, don't log typed commands \.
+ Use techniques to pass parameters not captured by the shell command history\. The following example shows how you can type the secret text into a text file, and then pass the file to the AWS Secrets Manager command and immediately destroy the file\. This means the typical shll history doesn't capture the secret text\. 

  The following example shows typical Linux commands but your shell might require slightly different commands:

  ```
  $ touch secret.txt                                                                           # Creates an empty text file
  $ chmod go-rx secret.txt                                                                     # Restricts access to the file to only the user
  $ cat > secret.txt                                                                           # Redirects standard input (STDIN) to the text file
  ThisIsMyTopSecretPassword^Z                                                                  # Everything the user types from this point up to the CTRL-D (^D) is saved in the file
  $ aws secretsmanager create-secret --name TestSecret --secret-string file://secret.txt       # The Secrets Manager command takes the --secret-string parameter from the contents of the file
  $ shred -u secret.txt                                                                        # The file is destroyed so it can no longer be accessed.
  ```

After you run these commands, you should be able to use the up and down arrows to scroll through the command history and see that the secret text isn't displayed on any line\.

**Important**  
By default, you can't perform an equivalent technique in Windows unless you first reduce the size of the command history buffer to **1**\.

**To configure the Windows Command Prompt to have only 1 command history buffer of 1 command**

1. Open an Administrator command prompt \(**Run as administrator**\)\.

1. Choose the icon in the upper left , and then choose **Properties**\.

1. On the **Options** tab, set **Buffer Size** and **Number of Buffers** both to **1**, and then choose **OK**\.

1. Whenever you have to type a command you don't want in the history, immediately follow it with one other command, such as:

   ```
   echo.
   ```

   This ensures you flush the sensitive command\. 

For the Windows Command Prompt shell, you can download the [SysInternals SDelete](https://docs.microsoft.com/en-us/sysinternals/downloads/sdelete) tool, and then use commands similar to the following:

```
C:\> echo. 2> secret.txt                                                                       # Creates an empty file
C:\> icacls secret.txt /remove "BUILTIN\Administrators" "NT AUTHORITY/SYSTEM" /inheritance:r   # Restricts access to the file to only the owner
C:\> copy con secret.txt /y                                                                    # Redirects the keyboard to text file, suppressing prompt to overwrite
THIS IS MY TOP SECRET PASSWORD^Z                                                             # Everything the user types from this point up to the CTRL-Z (^Z) is saved in the file
C:\> aws secretsmanager create-secret --name TestSecret --secret-string file://secret.txt      # The Secrets Manager command takes the --secret-string parameter from the contents of the file
C:\> sdelete secret.txt                                                                        # The file is destroyed so it can no longer be accessed.
```

## Cross\-Account Access – Should I Specify a User/Role or the Account?<a name="best-practice_cross-account-role-vs-account"></a>

When you want to use a resource\-based policy attached to a secret give access to an IAM principal in a different AWS account, you have two options:
+ **Specify only the other account ID** – In the `Principal` element of the statement, you specify the Amazon Resource Name \(ARN\) of the "foreign" account root\. This enables the administrator of the foreign account to delegate access to roles in the foreign account\. The administrator must then assign IAM permission policies to the role or roles that require access to the secret\.

  ```
  "Principal": {"AWS": arn:aws:iam::AccountId:root}
  ```
+ **Specify the exact user or role in the other account** – You specify an exact user or role ARN as the `Principal` of a secret\-based policy\. You get the ARN from the administrator of the other account\. Only the specific single user or role in the account can access the resource\.

  ```
  "Principal": [ 
      {"AWS": "arn:aws:iam::AccountId:role/MyCrossAccountRole"},
      {"AWS": "arn:aws:iam::AccountId:user/MyUserName"}
  ]
  ```

As a best practice, we recommend you specify only the account in the secret\-based policy, for the following reasons: 
+ In both cases, you trust the administrator of the other account\. In the first case, you trust the administrator to ensure only authorized individuals have access to the IAM user, or can assume the specified role\. This creates essentially the same level of trust as specifying only the account ID\. You trust the account and the administrator\. When you specify only the account ID, you give the administrator the flexibility to manage users\.
+ When you specify an exact role, IAM internally converts the role to a "principal ID" unique to the role\. If you delete the role and recreate it with the same name, the role receives a new principal ID\. This means the new role doesn't automatically obtain access to the resource\. IAM provides this functionality for security reasons, and means that an accidental delete and restore can result in "broken" access\.

When you grant permissions to only the account root, that set of permissions then becomes the limit of the options the administrator of the account can delegate to users and roles\. The administrator can't grant a permission to the resource if you didn't grant it to the account first\.

**Important**  
If you choose to grant cross\-account access directly to the secret without using a role, then the secret must be encrypted by using a custom AWS KMS customer master key \(CMK\)\. A principal from a different account must be granted permission to both the secret and the custom AWS KMS CMK\. 

## Running Everything in a VPC<a name="best-practice_using-a-vpc"></a>

Whenever possible, you should run as much of your infrastructure on private networks not accessible from the public internet\. To do this, host your servers and services in a virtual private cloud \(VPC\) provided by Amazon VPC\. AWS provides a virtualized private network accessible only to the resources in your account\. The public internet can't view or acccess, unless you explicitly configure it with access\. For example, you could add a NAT gateway\. For complete information about Amazon VPC, see the [Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)\.

To enable secret rotation within a VPC environment, perform these steps:

1. Configure your Lambda rotation function to run within the same VPC as the database server or service with a rotated secret\. For more information, see [Configuring a Lambda Function to Access Resources in an Amazon VPC](https://docs.aws.amazon.com/lambda/latest/dg/vpc.html) in the *AWS Lambda Developer Guide*\.

1. The Lambda rotation function, now running from within your VPC, must be able to access a Secrets Manager service endpoint\. If the VPC has no direct Internet connectivity, then you can configure your VPC with a private Secrets Manager endpoint accessible by all of the resources in your VPC\. For details, see [Configuring Your Network to Support Rotating Secrets](rotation-network-rqmts.md)\.

## Tag Your Secrets<a name="best-practice_tagging"></a>

Several AWS services enable you to add *tags* to your resources, and Secrets Manager allows you tag your secrets\. Secrets Manager defines a tag as a simple label consisting of a customer\-defined key and an optional value\. You can use the tags to make managing, searching, and filtering the resources in your AWS account\. When you tag your secrets, be sure to follow these guidelines:
+ Use a standardized naming scheme across all of your resources\. Remember tags are case sensitive\.
+ Create tag sets enabling you to perform the following:
  + **Security/access control** – You can grant or deny access to a secret by checking the tags attached to the secret\. 
  + **Cost allocation and tracking** – You can group and categorize your AWS bills by tags\. For more information, see [Using Cost Allocation Tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the *AWS Billing and Cost Management User Guide*\.
  + **Automation** – You can use tags to filter resources for automation activities\. For example, some customers run automated start/stop scripts to turn off development environments during non\-business hours to reduce costs\. You can create and then check for a tag indicating if a specific Amazon EC2 instance should be included in the shutdown\.
  + **AWS Management Console** – Some AWS service consoles enable you to organize the displayed resources according to the tags, and to sort and filter by tags\. AWS also provides the Resource Groups tool to create a custom console that consolidates and organizes your resources based on their tags\. For more information, see [Working with Resource Groups](https://docs.aws.amazon.com/) in the *AWS Management Console Getting Started Guide*\.

 Use tags creatively to manage your secrets\. Remember you must never store sensitive information for a secret in a tag\.

You can tag your secrets [when you create them](manage_create-basic-secret.md) or [when you edit them](manage_update-secret.md)\.

For more information, see [AWS Tagging Strategies](https://aws.amazon.com/answers/account-management/aws-tagging-strategies/) on the *AWS Answers* website\.

## Rotating Secrets on a Schedule<a name="best-practice-rotating-secrets"></a>

If you don't change your secrets for a long period of time, the secrets become more likely to be compromised\. As more users get access to a secret, it may be possible someone has mishandled and leaked it to an unauthorized entity\. Secrets can be leaked through logs and cache data\. They can be shared for debugging purposes and not changed or revoked once the debugging completes\. For all these reasons, secrets should be rotated frequently\. 

Configure Secrets Manager to rotate your secrets frequently to avoid using the same secrets for your applications\.

See [Rotating Your AWS Secrets Manager Secrets](rotating-secrets.md)

## Auditing Access to Secrets<a name="w71aac11c23"></a>

As a best practice, you should monitor your secrets for usage and log any changes to them\. This helps you to ensure you can investigate any unexpected usage or change, and unwanted changes can be rolled back\. 

You can monitor access to secrets using AWS CloudTrail to log all activity for Secrets Manager\. In addition, you should set automated checks for inappropriate usage of secrets\. 

See [Monitoring the Use of Your AWS Secrets Manager Secrets](monitoring.md)

## Monitoring Your Secrets with AWS Config<a name="aws-config"></a>

AWS Secrets Manager integrates with AWS Config and provides easier tracking of secret changes in Secrets Manager\. AWS Config can help you define your organizational internal guidelines on secrets management best practices\. Also, you can quickly identify secrets that don't conform to your security rules as well as receive notifications from Amazon SNS about your secrets configuration changes\. 

See [Monitoring Secrets Manager Secrets Using AWS Config](integrating_awsconfig.md)