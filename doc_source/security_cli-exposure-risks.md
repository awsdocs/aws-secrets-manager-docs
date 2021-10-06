# Mitigate the risks of using the AWS CLI to store your secrets<a name="security_cli-exposure-risks"></a>

When you use the AWS Command Line Interface \(AWS CLI\) to invoke AWS operations, you enter those commands in a command shell\. For example, you can use the Windows command prompt or Windows PowerShell, or the Bash or Z shell, among others\. Many of these command shells include functionality designed to increase productivity\. But this functionality can be used to compromise your secrets\. For example, in most shells, you can use the up arrow key to see the last entered command\. The *command history* feature can be exploited by anyone who accesses your unsecured session\. Also, other utilities that work in the background might have access to your command parameters, with the intended goal of helping you perform your tasks more efficiently\. To mitigate such risks, ensure you take the following steps:
+ Always lock your computer when you walk away from your console\.
+ Uninstall or disable console utilities you don't need or no longer use\.
+ Ensure the shell or the remote access program, if you are using one or the other, don't log typed commands \.
+ Use techniques to pass parameters not captured by the shell command history\. The following example shows how you can type the secret text into a text file, and then pass the file to the AWS Secrets Manager command and immediately destroy the file\. This means the typical shell history doesn't capture the secret text\. 

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