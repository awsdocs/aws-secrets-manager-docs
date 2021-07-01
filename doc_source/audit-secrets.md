# Auditing access to secrets<a name="audit-secrets"></a>

As a best practice, you should monitor your secrets for usage and log any changes to them\. This helps you to ensure you can investigate any unexpected usage or change, and unwanted changes can be rolled back\. 

You can monitor access to secrets using AWS CloudTrail to log all activity for Secrets Manager\. In addition, you should set automated checks for inappropriate usage of secrets\. 

See [Monitoring the use of your AWS Secrets Manager secrets](monitoring.md)

You can also configure CloudWatch to notify you of any attempts to delete secrets\. 