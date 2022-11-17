# Schedule expressions in Secrets Manager rotation<a name="rotate-secrets_schedule"></a>

When you turn on automatic rotation, you can use a **cron\(\)** or **rate\(\)** expression to set the schedule for rotating your secret\. With a rate expression, you can create a rotation schedule that repeats on an interval of hours or days\. With a cron expression, you can create rotation schedules that are more detailed than a rotation interval\. Secrets Manager rotation schedules use UTC time zone\. You can rotate a secret as often as every four hours\. Secrets Manager rotates your secret at any time during the rotation window\.

To turn on rotation, see:
+ [Set up automatic rotation for Amazon RDS, Amazon Redshift, or Amazon DocumentDB secrets using the console](rotate-secrets_turn-on-for-db.md)
+ [Set up automatic rotation for AWS Secrets Manager secrets using the console](rotate-secrets_turn-on-for-other.md)
+ [Set up automatic rotation for AWS Secrets Manager secrets using the AWS CLI](rotate-secrets-cli.md)

## Rate expressions<a name="rotate-secrets_schedule-rate"></a>

Secrets Manager rate expressions have the following format, where *Value* is a positive integer and *Unit* can be `hour`, `hours`, `day`, or `days`:

```
rate(Value Unit)
```

You can rotate a secret as often as every four hours\. Examples:
+ `rate(4 hours)` means the secret is rotated every four hours\.
+ `rate(1 day)` means the secret is rotated every day\.
+ `rate(10 days)` means the secret is rotated every 10 days\.

For a rate in *hours*, the default rotation window starts at midnight and closes after one hour\. You can set the **Window duration** to change the rotation window\. The rotation window must not extend into the next rotation window\. One way to check this is to confirm that the rotation window is less than or equal to the number of hours between rotations\.

For a rate in *days*, the default rotation window starts at midnight and closes at the end of the day\. You can set the **Window duration** to change the rotation window\. The rotation window must not extend into the next UTC day\. One way to check this is to confirm that the start hour plus the window duration is less than or equal to 24 hours\. 

## Cron expressions<a name="rotate-secrets_schedule-cron"></a>

Cron expressions have the following format:

```
cron(Minutes Hours Day-of-month Month Day-of-week Year)
```

A cron expression that includes increments of hours resets each day\. For example, `cron(0 4/12 * * ? *)` means 4:00 AM, 4:00 PM, and then the next day 4:00 AM, 4:00 PM\. Secrets Manager rotation schedules use UTC time zone\.

For a schedule in hours, the default rotation window closes after one hour\. You can set the **Window duration** to change the rotation window\. The rotation window must not go into the next rotation window\. 


| Example schedule | Expression | 
| --- | --- | 
| Every eight hours starting at midnight\.  |  `cron(0 /8 * * ? *)`  | 
| Every eight hours starting at 8:00 AM\.  |  `cron(0 8/8 * * ? *)`  | 
| Every ten hours, starting at 2:00 AM\. The rotation windows will start at 2:00, 12:00, and 22:00, and then the next day at 2:00, 12:00, and 22:00\.  |  `cron(0 2/10 * * ? *)`  | 
| Every day at 10:00 AM\. |  `cron(0 10 * * ? *)`  | 
|  Every Saturday at 6:00 PM\.  |  `cron(0 18 ? * SAT *)`  | 
|  The first day of every month at 8:00 AM\.  |  `cron(0 8 1 * ? *)`  | 
|  Every three months on the first Sunday at 1:00 AM\.  |  `cron(0 1 ? 1/3 SUN#1 *)`  | 
|  The last day of every month at 5:00 PM\.  |  `cron(0 17 L * ? *)`  | 
|  Monday through Friday at 8:00 AM\.  |  `cron(0 8 ? * MON-FRI *)`  | 
|  First and 15th day of every month at 4:00 PM\.  |  `cron(0 16 1,15 * ? *)`  | 
|  First Sunday of every month at midnight\.  |  `cron(0 0 ? * SUN#1 *)`  | 

### Cron expression requirements in Secrets Manager<a name="rotate-secrets_schedule-cron-ASM"></a>

Secrets Manager has some restrictions on what you can use for cron expressions\. A cron expression for Secrets Manager must have **0** in the minutes field because Secrets Manager rotation windows start on the hour\. It must have **\*** in the year field, because Secrets Manager does not support rotation schedules that are more than a year apart\. The following table shows the options you can use\.


| **Fields** | **Values** | **Wildcards** | 
| --- | --- | --- | 
|  Minutes  | Must be 0 | None | 
|  Hours  |  0–23  |  Use **/** \(forward slash\) to specify increments\. For example `2/10` means every 10 hours beginning at 2:00 AM\.   | 
|  Day\-of\-month  |  1–31  |  Use **,** \(comma\) to include additional values\. For example `1,15` means the first and 15th day of the month\. Use **\-** \(dash\) to specify a range\. For example `1–15` means days 1 through 15 of the month\. Use **\*** \(asterisk\) to includes all values in the field\. For example `*` means every day of the month\. The **?** \(question mark\) wildcard specifies one or another\. You can't specify the `Day-of-month` and `Day-of-week` fields in the same cron expression\. If you specify a value in one of the fields, you must use a **?** \(question mark\) in the other\. Use **/** \(forward slash\) to specify increments\. For example, `1/2` means every two days starting on day 1, in other words, day 1, 3, 5, and so on\. Use **L** to specify the last day of the month\. Use ***DAY*L** to specify the last named day of the month\. For example `SUNL` means the last Sunday of the month\.  | 
|  Month  |  1–12 or JAN–DEC  |  Use **,** \(comma\) to include additional values\. For example, `JAN,APR,JUL,OCT` means January, April, July, and October\. Use **\-** \(dash\) to specify a range\. For example `1–3` means months 1 through 3 of the year\. Use **\*** \(asterisk\) to includes all values in the field\. For example `*` means every month\. Use **/** \(forward slash\) to specify increments\. For example, `1/3` means every third month, starting on month 1, in other words month 1, 4, 7, and 10\.  | 
|  Day\-of\-week  |  1–7 or SUN–SAT  |  Use **\#** to specify the day of the week within a month\. For example, `TUE#3` means the third Tuesday of the month\.  Use **,** \(comma\) to include additional values\. For example `1,4` means the first and fourth day of the week\. Use **\-** \(dash\) to specify a range\. For example `1–4` means days 1 through 4 of the week\. Use **\*** \(asterisk\) to includes all values in the field\. For example `*` means every day of the week\. The **?** \(question mark\) wildcard specifies one or another\. You can't specify the `Day-of-month` and `Day-of-week` fields in the same cron expression\. If you specify a value in one of the fields, you must use a **?** \(question mark\) in the other\. Use **/** \(forward slash\) to specify increments\. For example, `1/2` means every second day of the week, starting on the first day, so day 1, 3, 5, and 7\. Use **L** to specify the last day of the week\.  | 
|  Year  | Must be \* | None | 