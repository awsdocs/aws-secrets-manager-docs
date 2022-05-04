# Schedule expressions in Secrets Manager rotation<a name="rotate-secrets_schedule"></a>

When you turn on automatic rotation, you can use a **cron\(\)** or **rate\(\)** expression to set the schedule for rotating your secret\. With a rate expression, you can create a rotation schedule that repeats on an interval of days\. With a cron expression, you can create rotation schedules that are more detailed than a rotation interval\. Secrets Manager rotation schedules use UTC time zone\. 

Secrets Manager rotates your secret at any time during a *rotation window*\. For a `rate()` expression, the rotation window automatically starts at midnight\. For a `cron()` expression, the rotation window opens at the time you specify\. By default, the rotation window closes at the end of the day\. You can set a **Window duration** to shorten the rotation window\. The rotation window must not go into the next UTC day, or in other words, the start hour plus the window duration must be less than or equal to 24 hours\.

To turn on rotation, see:
+  [Automatically rotate an Amazon RDS, Amazon DocumentDB, or Amazon Redshift secret](rotate-secrets_turn-on-for-db.md)
+ [Automatically rotate a secret](rotate-secrets_turn-on-for-other.md)

## Rate expressions<a name="rotate-secrets_schedule-rate"></a>

Secrets Manager rate expressions have the following format, where *Value* is a positive integer and *Unit* can be `day` or `days`:

```
rate(Value Unit)
```

For example, `rate(8 days)` means the secret is rotated every eight days\.

## Cron expressions<a name="rotate-secrets_schedule-cron"></a>

Cron expressions have the following format:

```
cron(Minutes Hours Day-of-month Month Day-of-week Year)
```


| **Fields** | **Values** | **Wildcards** | 
| --- | --- | --- | 
|  Minutes  | Must be 0 |  | 
|  Hours  |  0–23  |  | 
|  Day\-of\-month  |  1–31  |  , \- \* ? / L  | 
|  Month  |  1–12 or JAN–DEC  |  , \- \* /  | 
|  Day\-of\-week  |  1–7 or SUN–SAT  |  \# , \- \* ? / L  | 
|  Year  | Must be \* |  | 

A cron expression for Secrets Manager must have **0** in the minutes field because Secrets Manager rotation windows open on the hour\. It must have **\*** in the year field, because Secrets Manager does not support rotation schedules that are more than a year apart\.

**Wildcards:**
+ The **\*** \(asterisk\) wildcard includes all values in the field\. In the `Days` field, **\*** means every day\.
+ The **,** \(comma\) wildcard includes additional values\. In the `Month` field, `JAN,APR,JUL,OCT` means January, April, July, and October\.
+ The **\-** \(dash\) wildcard specifies ranges\. In the `Day` field, `1–15` means days 1 through 15 of the specified month\.
+ The **/** \(forward slash\) wildcard specifies increments\. In the `Days` field, `1/10` means every 10 days\.
+ The **?** \(question mark\) wildcard specifies one or another\. You can't specify the `Day-of-month` and `Day-of-week` fields in the same cron expression\. If you specify a value in one of the fields, you must use a **?** \(question mark\) in the other\.
+ The **L** wildcard in the `Day-of-month` or `Day-of-week` fields specifies the last day of the month or week\. In `Day-of-month`, you can also specify the last named day of the month, for example SUNL means the last Sunday of the month\.
+ The **\#** wildcard in the `Day-of-week` field specifies the day of the week within a month\. For example, `TUE#3` means the third Tuesday of the month\. 

### Examples<a name="rotate-secrets_cron-examples"></a>


| Schedule \(UTC\) | Expression | 
| --- | --- | 
| Every day at 10:00 AM\. |  `cron(0 10 * * ? *)`  | 
|  Every Saturday at 6:00 PM\.  |  `cron(0 18 ? * SAT *)`  | 
|  The first day of every month at 8:00 AM\.  |  `cron(0 8 1 * ? *)`  | 
|  Every three months on Sunday at 1:00 AM\.  |  `cron(0 1 * 1/3 SUN *)`  | 
|  The last day of every month at 5:00 PM\.  |  `cron(0 17 L * ? *)`  | 
|  Monday through Friday at 8:00 AM\.  |  `cron(0 8 ? * MON-FRI *)`  | 
|  First and 15th day of every month at 4:00 PM\.  |  `cron(0 16 1,15 * ? *)`  | 
|  First Sunday of every month at midnight\.  |  `cron(0 0 ? * SUN#1 *)`  | 