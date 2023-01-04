# Managed rotation for AWS Secrets Manager secrets<a name="rotate-secrets_managed"></a>

Some services offer *managed rotation*, where the service configures and manages rotation for you\. With managed rotation, you don't use an AWS Lambda function to update the secret and the credentials in the database\. The following services offer managed rotation:
+ Amazon RDS offers managed rotation for master user credentials\. For more information, see [Password management with Amazon RDS and AWS Secrets Manager](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-secrets-manager.html) in the *Amazon RDS User Guide*\.
+ Aurora offers managed rotation for master user credentials\. For more information, see [Password management with Amazon Aurora and AWS Secrets Manager](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/rds-secrets-manager.html) in the *Amazon Aurora User Guide*\.

For all other types of secrets, see [Rotate secrets](rotating-secrets.md)\.

**To change the schedule for managed rotation \(console\)**

1. Open the managed secret in the Secrets Manager console\. You can follow a link from the managing service, or [search for the secret](service-linked-secrets.md) in the Secrets Manager console\.

1. Under **Rotation schedule**, enter your schedule in UTC time zone in either the **Schedule expression builder** or as a **Schedule expression**\. Secrets Manager stores your schedule as a `rate()` or `cron()` expression\. The rotation window automatically starts at midnight unless you specify a **Start time**\. For more information, see [Schedule expressions](rotate-secrets_schedule.md)\.

1. \(Optional\) For **Window duration**, choose the length of the window during which you want Secrets Manager to rotate your secret, for example **3h** for a three hour window\. The window must not extend into the next rotation window\. If you don't specify **Window duration**, for a rotation schedule in hours, the window automatically closes after one hour\. For a rotation schedule in days, the window automatically closes at the end of the day\. 

1. Choose **Save**\.

**To change the schedule for managed rotation \(AWS CLI\)**
+ Call [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/rotate-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/rotate-secret.html)\. The following example rotates the secret between 16:00 and 18:00 UTC on the 1st and 15th day of the month\. For more information, see [Schedule expressions](rotate-secrets_schedule.md)\.

  ```
  aws secretsmanager rotate-secret \
      --secret-id MySecret \
      --rotation-rules "{\"ScheduleExpression\": \"cron(0 16 1,15 * ? *)\", \"Duration\": \"2h\"}"
  ```