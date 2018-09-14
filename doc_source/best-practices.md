# AWS Secrets Manager Best Practices<a name="best-practices"></a>

The following recommendations help you to more securely use AWS Secrets Manager:

**Topics**
+ [Protect Additional Sensitive Information](#best-practice_what-not-to-put-in-secret-text)
+ [Mitigate the Risks of Logging and Debugging Your Lambda Function](#best-practice_lamda-debug-statements)
+ [Mitigate the Risks of Using the AWS CLI to Store Your Secrets](#best-practice_cli-exposure-risks)
+ [Cross\-Account Access – Should I Specify a User/Role or the Account?](#best-practice_cross-account-role-vs-account)
+ [Run Everything in a VPC](#best-practice_using-a-vpc)

## Protect Additional Sensitive Information<a name="best-practice_what-not-to-put-in-secret-text"></a>

A secret often includes several pieces of information beyond the user name and password\. Depending on the database, service, or website, you can choose to include additional sensitive data\. This data can include password hints, or question\-and\-answer pairs that you can use to recover your password if you forgot it\.

Make sure that any information that might be used to gain access to the credentials in the secret is protected as securely as the credentials themselves\. Don't store such information in the `Description` or any other non\-encrypted part of the secret\.

Instead, store all such sensitive information as part of the encrypted secret value \(in either the `SecretString` or `SecretBinary` field\)\. You can store up to 4096 characters in the secret\. In the `SecretString` field, the text usually takes the form of JSON key\-value string pairs, as shown in the following example:

```
{
  "username": "saanvisarkar",
  "password": "i29wwX!%9wFV",
  "host": "http://myserver.example.com",
  "port": "1234",
  "database": "myDatabase"
}
```

## Mitigate the Risks of Logging and Debugging Your Lambda Function<a name="best-practice_lamda-debug-statements"></a>

When you create a custom Lambda rotation function to support your Secrets Manager secret, be careful about including debugging or logging statements in your function\. These statements can cause information in your function to be written to CloudWatch\. Ensure that this logging of information to CloudWatch doesn't include any of the sensitive data that's stored in the encrypted secret value\. Also, if you do choose to include any such statements in your code during development for testing and debugging purposes, make sure that you remove those lines from the code before it's used in production\. Also, remember to remove any logs that include sensitive information that was collected during development after it's no longer needed\.

These kinds of logging and debug statements aren't included in any of the Lambda functions that AWS provides for [supported databases](intro.md#full-rotation-support)\.

## Mitigate the Risks of Using the AWS CLI to Store Your Secrets<a name="best-practice_cli-exposure-risks"></a>

When you use the AWS Command Line Interface \(AWS CLI\) to invoke AWS operations, you enter those commands in a command shell\. For example, you can use the Windows command prompt or Windows PowerShell, or the Bash or Z shell, among others\. Many of these command shells include functionality that's designed to increase productivity\. But this functionality could be used to compromise your secrets\. For example, in most shells, you can use the up arrow key to see the last command that was entered\. This *command history* feature could be exploited by anyone who walks up to your unsecured session\. Also, other utilities that work in the background might have access to your command parameters \(with the intended goal of helping you get your tasks done more efficiently\)\. To mitigate such risks, ensure that you take the following steps:
+ Always lock your computer when you walk away from your console\.
+ Uninstall or disable console utilities that you don't need or no longer use\.
+ Ensure that both the shell and the remote access program \(if you are using one\) don't log the commands you type\.
+ Use techniques to pass parameters that aren't captured by shell's command history\. The following example shows how you can type the secret text into a text file, which is then passed to the AWS Secrets Manager command and immediately destroyed\. This means that the secret text isn't captured by the typical shell history\. 

  The following example shows typical Linux commands \(your shell might require slightly different commands\):

  ```
  $ touch secret.txt                                                                           # Creates an empty text file
  $ chmod go-rx secret.txt                                                                     # Restricts access to the file to only the user
  $ cat > secret.txt                                                                           # Redirects standard input (STDIN) to the text file
  ThisIsMyTopSecretPassword^Z                                                                  # Everything the user types from this point up to the CTRL-Z (^Z) is saved in the file
  $ aws secretsmanager create-secret --name TestSecret --secret-string file://secret.txt       # The Secrets Manager command takes the --secret-string parameter from the contents of the file
  $ shred -u secret.txt                                                                        # The file is destroyed so it can no longer be accessed.
  ```

After you run these commands, you should be able to use the up and down arrows to scroll through the command history and see that the secret text isn't displayed on any line\.

**Important**  
By default, you can't perform an equivalent technique in Windows unless you first reduce the size of the command history buffer to **1**\.

**To configure the Windows Command Prompt to have only 1 command history buffer of 1 command**

1. Open an Administrator command prompt \(**Run as administrator**\)\.

1. Choose the icon in the upper left on the title bar, and then choose **Properties**\.

1. On the **Options** tab, set **Buffer Size** and **Number of Buffers** both to **1**, and then choose **OK**\.

1. Whenever you have to type a command that you don't want in the history, immediately follow it with one other command, such as:

   ```
   echo.
   ```

   This ensures that the sensitive command is flushed\.

For the Windows Command Prompt shell, you can download the [SysInternals SDelete](https://docs.microsoft.com/en-us/sysinternals/downloads/sdelete) tool, and then use commands that are similar to the following:

```
C:\> echo. 2> secret.txt                                                                       # Creates an empty file
C:\> icacls secret.txt /remove "BUILTIN\Administrators" "NT AUTHORITY/SYSTEM" /inheritance:r   # Restricts access to the file to only the owner
C:\> copy con secret.txt /y                                                                    # Redirects the keyboard to text file, suppressing prompt to overwrite
THIS IS MY TOP SECRET PASSWORD^Z                                                             # Everything the user types from this point up to the CTRL-Z (^Z) is saved in the file
C:\> aws secretsmanager create-secret --name TestSecret --secret-string file://secret.txt      # The Secrets Manager command takes the --secret-string parameter from the contents of the file
C:\> sdelete secret.txt                                                                        # The file is destroyed so it can no longer be accessed.
```

## Cross\-Account Access – Should I Specify a User/Role or the Account?<a name="best-practice_cross-account-role-vs-account"></a>

When you want to use a resource\-based policy that's attached to a secret to grant access to an IAM principal in a different AWS account, you have two options:
+ **Specify only the other account ID** – In the `Principal` element of the statement, you specify the Amazon Resource Name \(ARN\) of the "foreign" account's root\. This enables the administrator of the foreign account to delegate access to roles in the foreign account\. The administrator must then assign IAM permission policies to the role or roles that must be able to access the secret\.

  ```
  "Principal": {"AWS": arn:aws:iam::AccountId:root}
  ```
+ **Specify the exact user or role in the other account** – You specify an exact user or role ARN as the `Principal` of a secret\-based policy\. You get the ARN from the administrator of the other account\. Only that single user or role in the account is able to access the resource\.

  ```
  "Principal": [ 
      {"AWS": "arn:aws:iam::AccountId:role/MyCrossAccountRole"},
      {"AWS": "arn:aws:iam::AccountId:user/MyUserName"}
  ]
  ```

As a best practice, we recommend that you specify only the account in the secret\-based policy, for the following reasons: 
+ In both cases, you're trusting the administrator of the other account\. In the first case, you trust the administrator to ensure that only authorized individuals have access to the IAM user, or can assume the specified role\. This is essentially the same level of trust as specifying only the account ID\. You trust that account and its administrator\. When you specify only the account ID, you give the administrator the flexibility to manage their users as they see fit\.
+ When you specify an exact role, it's internally converted to a "principal ID" that's unique to that role\. If you delete the role and recreate it with the same name, it gets a new principal ID\. This means that the new role doesn't automatically get access to the resource\. This functionality is for security reasons, but it means that an accidental delete and restore can result in "broken" access\.

When you grant permissions to only the account root, that set of permissions then becomes the limit of what the administrator of that account can delegate to their users and roles\. The administrator can't grant a permission to the resource that you didn't grant to the account first\.

**Important**  
If you choose to grant cross\-account access directly to the secret without using a role, then the secret must be encrypted by using a custom AWS KMS customer master key \(CMK\)\. A principal from a different account must be granted permission to both the secret and the custom AWS KMS CMK\. 

## Run Everything in a VPC<a name="best-practice_using-a-vpc"></a>

Whenever possible, you should run as much of your infrastructure on private networks that aren't accessible from the public internet\. To do this, host your servers and services in a virtual private cloud \(VPC\) provided by Amazon VPC\. This is a virtualized private network that's accessible only to the resources in your account\. It's not visible to or accessible by the public internet, unless you explicitly configure it with access\. For example, you could add a NAT gateway\. For complete information about Amazon VPC, see the [Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)\.

To enable secret rotation within a VPC environment, perform these steps:

1. Configure your Lambda rotation function to run within the same VPC as the database server or service whose secret is rotated\. For more information, see [Configuring a Lambda Function to Access Resources in an Amazon VPC](https://docs.aws.amazon.com/lambda/latest/dg/vpc.html) in the *AWS Lambda Developer Guide*\.

1. The Lambda rotation function, which is now running from within your VPC, must be able to access a Secrets Manager service endpoint\. If the VPC has no direct internet connectivity, then you can configure your VPC with a private Secrets Manager endpoint that can be accessed by all of the resources in your VPC\. For details, see [Configuring Your Network to Support Rotating Secrets](rotation-network-rqmts.md)\.