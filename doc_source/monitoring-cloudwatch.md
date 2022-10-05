# Monitoring AWS Secrets Manager with Amazon CloudWatch<a name="monitoring-cloudwatch"></a>

You can monitor AWS Secrets Manager using Amazon CloudWatch, which collects raw data and processes it into readable, near real\-time metrics\. These statistics are kept for 15 months, so that you can access historical information and gain a better perspective on how your web application or service is performing\. You can also set alarms that watch for certain thresholds, and send notifications or take actions when those thresholds are met\. For more information, see the [Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)\.

For Secrets Manager, you can use CloudWatch to alert you when your request rate for APIs or the number of secrets in your account reaches a specific threshold\. You can also use CloudWatch to monitor estimated Secrets Manager charges\. For more information, see [Creating a billing alarm to monitor your estimated AWS charges](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/monitor_estimated_charges_with_cloudwatch.html)\.

**Topics**
+ [Secrets Manager metrics and dimensions](#monitoring-cloudwatch_alarms_secretcount)
+ [Create alarms to monitor Secrets Manager metrics](#monitoring-cloudwatch_alarms)
+ [Secrets Manager events](#monitoring-cloudwatch_events)
+ [Amazon CloudWatch Synthetics canaries](#monitoring-cloudwatch_canaries)
+ [Monitor AWS Secrets Manager secrets scheduled for deletion by using Amazon CloudWatch](monitoring_cloudwatch_deleted-secrets.md)

## Secrets Manager metrics and dimensions<a name="monitoring-cloudwatch_alarms_secretcount"></a>

The `AWS/SecretsManager` namespace includes the following metrics\.


| Metric | Description | 
| --- | --- | 
|  `ResourceCount`  |  The number of secrets in your account, including secrets that are marked for deletion\. The metric is published hourly\. Units: Count  | 

Dimensions for the Secrets Manager metrics\.


|  Dimension  |  Description  | 
| --- | --- | 
|  Service  | The name of the AWS service containing the resource\. For Secrets Manager, the value for this dimention is Secrets Manager\. | 
|  Type  | The type of entity that is being reported\. For Secrets Manager, the value for this dimention is Resource\. | 
|  Resource  | The type of resource that is running\. For Secrets Manager, the value for this dimention is SecretCount\. | 
|  Class  | None\. | 

Secrets Manager API requests that you can monitor using CloudWatch metrics include `GetSecretValue`, `DescribeSecret`, `ListSecrets`, and others\. To find metrics, in the CloudWatch console, choose **All metrics**, and then in the search box, enter your search term, for example **secrets**\.

## Create alarms to monitor Secrets Manager metrics<a name="monitoring-cloudwatch_alarms"></a>

You can create a CloudWatch alarm that sends an Amazon SNS message when the value of the metric changes and causes the alarm to change state\. An alarm watches a metric over a time period you specify, and performs actions based on the value of the metric relative to a given threshold over a number of time periods\. Alarms invoke actions for sustained state changes only\. CloudWatch alarms do not invoke actions simply because they are in a particular state; the state must have changed and been maintained for a specified number of periods\. 

For more information, see [ Using Amazon CloudWatch alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html)\.

## Secrets Manager events<a name="monitoring-cloudwatch_events"></a>

Secrets Manager integrates with Amazon EventBridge to notify you of certain events that affect your secrets\. With Amazon EventBridge, you can be notified of Secrets Manager events that happen except `Get*` API calls\. You can configure EventBridge rules that look for these events and then send new generated events to an Amazon SNS topic that emails or text messages to subscribers\. 

For more information, see [Creating Amazon EventBridge rules that react to events](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-create-rule.html)\.

## Amazon CloudWatch Synthetics canaries<a name="monitoring-cloudwatch_canaries"></a>

Amazon CloudWatch Synthetics canaries are configurable scripts that run on a schedule to monitor your endpoints and APIs\. Canaries follow the same routes and perform the same actions as a customer, which makes it possible for you to continually verify your customer experience even when you don't have any customer traffic on your applications\. 

For an example of how to integrate Secrets Manager, see [Integrating your canary with other AWS services](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Synthetics_Canaries_WritingCanary_Nodejs.html#CloudWatch_Synthetics_Canaries_AWS_integrate)\.