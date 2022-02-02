# Secrets Manager best practices<a name="best-practices"></a>

The following recommendations help you to more securely use AWS Secrets Manager:

**Add retries to your application**  
Your AWS client might see calls to Secrets Manager fail due to rate limiting\. When you exceed an API request quota, Secrets Manager throttles the request\. To respond, use a backoff and retry strategy\. See [Add retries to your application](quotas_throttling.md)\.

**Mitigate the risks of logging and debugging your Lambda function**  
When you create a Lambda rotation function, be cautious about including debugging or logging statements in your function\. These statements can cause information in your function to be written to Amazon CloudWatch, so make sure the log doesn't include any sensitive data from the secret\. If you do include these statements in your code for testing and debugging, make sure you remove them before using the code in production\. Also remove any logs that include sensitive information collected during development\.  
The Lambda functions for [supported databases](intro.md#full-rotation-support) don't include logging and debug statements\. 

**Mitigate the risks of using the AWS CLI to store your secrets**  
When you use the AWS CLI and enter commands in a command shell, there is a risk of the command history being accessed or utilities having access to your command parameters\. See [Mitigate the risks of using the AWS CLI to store your secrets](security_cli-exposure-risks.md)\.

**Run everything in a VPC **  
We recommend that you run as much of your infrastructure as possible on private networks that are not accessible from the public internet\. See [Running everything in a VPC ](integrating_vpc.md)\.

**Rotate secrets on a schedule**  
If you don't change your secrets for a long period of time, the secrets become more likely to be compromised\. We recommend that you rotate your secrets every 30 days\. See [Rotate AWS Secrets Manager secrets](rotating-secrets.md)

**Monitor your secrets**  
Monitor your secrets and log any changes to them\. You can use the logs if you need to investigate any unexpected usage or change, and then you can roll back unwanted changes\. You can also set automated checks for inappropriate usage of secrets and any attempts to delete secrets\. See [Monitor AWS Secrets Manager secrets](monitoring.md)\.

**Use Secrets Manager to provide credentials to Lambda functions**  
Use Secrets Manager to securely provide database credentials to Lambda functions without hardcoding the secrets in code or passing them through environmental variables\. See [How to securely provide database credentials to Lambda functions by using AWS Secrets Manager](https://aws.amazon.com/blogs/security/how-to-securely-provide-database-credentials-to-lambda-functions-by-using-aws-secrets-manager/)\.

**More resources on best practices**  
For more resources, see [ Security Pillar \- AWS Well\-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html)\.