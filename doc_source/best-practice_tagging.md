# Tag your secrets<a name="best-practice_tagging"></a>

Several AWS services enable you to add *tags* to your resources, and Secrets Manager allows you tag your secrets\. Secrets Manager defines a tag as a simple label consisting of a customer\-defined key and an optional value\. You can use the tags to make it easy to manage, search, and filter the resources in your AWS account\. When you tag your secrets, be sure to follow these guidelines:
+ Use a standardized naming scheme across all of your resources\. Remember tags are case sensitive\.
+ Create tag sets enabling you to perform the following:
  + **Security/access control** – You can grant or deny access to a secret by checking the tags attached to the secret\. 
  + **Cost allocation and tracking** – You can group and categorize your AWS bills by tags\. For more information, see [Using Cost Allocation Tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the *AWS Billing and Cost Management User Guide*\.
  + **Automation** – You can use tags to filter resources for automation activities\. For example, some customers run automated start/stop scripts to turn off development environments during non\-business hours to reduce costs\. You can create and then check for a tag indicating if a specific Amazon EC2 instance should be included in the shutdown\.
  + **AWS Management Console** – Some AWS service consoles enable you to organize the displayed resources according to the tags, and to sort and filter by tags\. AWS also provides the Resource Groups tool to create a custom console that consolidates and organizes your resources based on their tags\. For more information, see [Working with Resource Groups](https://docs.aws.amazon.com/) in the *AWS Management Console Getting Started Guide*\.

 Use tags creatively to manage your secrets\. Remember you must never store sensitive information for a secret in a tag\.

You can tag your secrets [when you create them](manage_create-basic-secret.md) or [when you edit them](manage_update-secret.md)\.

For more information, see [AWS Tagging Strategies](https://aws.amazon.com/answers/account-management/aws-tagging-strategies/) on the *AWS Answers* website\.

