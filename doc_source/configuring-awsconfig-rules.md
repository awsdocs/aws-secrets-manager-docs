# Configuring AWS Config Secrets Manager Rules<a name="configuring-awsconfig-rules"></a>
**Important**  
If using the AWS Config console for the first time, see [Setting Up AWS Config \(Console\)](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html)\.

The Rules page provides initial AWS managed rules you can add to your account\. After set up, AWS Config evaluates your AWS Secrets Manager resources against your selected rules\. You can update the rules and create additional managed rules after set up\. 

1. Log into the AWS Config console at [https://console\.aws\.amazon\.com/config/](https://console.aws.amazon.com/config/)\.

1. Choose **Settings**\. Be sure you enable the parameter **Recording is on**\.

1. Choose **Rules**\.

1. In the **Rules** section, choose **Add Rule**\.

1. Type **secretsmanager\-rotation\-enabled\-check** in the filter field\.

1. To configure the `secretsmanager-rotation-enabled-check` rule, choose **Rules** from the console panel, and then choose **Add rule**\.

1. Locate the rule, `secretsmanager-rotation-enabled-check` using the search function\.

1. Create a unique name for the rule such as *MySecretsRotationRule*\.

1. Specify a **Remedial Action** to receive notification about noncompliant secrets using a Amazon SNS topic\.

1. Specify a topic for the Amazon SNS notification\.

1. Choose **Save** to store the rule in AWS Config

Once you save the rule, AWS Config evaluates your secrets every time the metadata of a secret changes\. If changes occur, you receive an SNS notification about noncompliant secrets\. You can also view the results from the **Rules** or **Dashboard** of AWS Config\.

If you choose the **secretsmanager\-rotation\-check\-mySecretsRotationRule** from the list of rules, then AWS Config displays a list of secrets in your account not configured for rotation\. Because you identified the secrets, you can begin implementation of your best practices for secret rotation\.
+ See [AWS Config documentation](https://docs.aws.amazon.com/config/latest/developerguide/secretsmanager-rotation-enabled-check.html) on the `secretsmanager-rotation-enabled-check`\.
+ See [AWS Config documentation](https://docs.aws.amazon.com/config/latest/developerguide/secretsmanager-scheduled-rotation-success-check.html) on the `secretsmanager-scheduled-rotation-enabled-success-check`\.