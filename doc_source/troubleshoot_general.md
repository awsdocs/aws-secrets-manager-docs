# Troubleshooting General Issues<a name="troubleshoot_general"></a>

Use the information here to help you diagnose and fix access\-denied or other common issues that you might encounter when you're working with AWS Secrets Manager\.

**Topics**
+ [I receive an "access denied" message when I send a request to AWS Secrets Manager\.](#troubleshoot_general_access-denied-service)
+ [I receive an "access denied" message when I send a request with temporary security credentials\.](#troubleshoot_general_access-denied-temp-creds)
+ [Changes I make aren't always immediately visible\.](#troubleshoot_general_eventual-consistency)
+ [I receive a “cannot generate a data key with an asymmetric CMK” message when creating a secret\.](#asymmetrical-key)

## I receive an "access denied" message when I send a request to AWS Secrets Manager\.<a name="troubleshoot_general_access-denied-service"></a>
+ Verify that you have permissions to call the operation and resource you requested\. An administrator must grant permissions by attaching an IAM policy to your IAM user, or to a group that you're a member of\. If the policy statements that grant those permissions include any conditions, such as time\-of\-day or IP address restrictions, you also must meet those requirements when you send the request\. For information about viewing or modifying policies for an IAM user, group, or role, see [Working with Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage.html) in the *IAM User Guide*\.
+ If you're signing API requests manually, without using the [AWS SDKs](http://aws.amazon.com/tools/), verify you correctly [signed the request](https://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html)\.

## I receive an "access denied" message when I send a request with temporary security credentials\.<a name="troubleshoot_general_access-denied-temp-creds"></a>
+ Verify the IAM user or role you're using to make the request has the correct permissions\. Permissions for temporary security credentials derive from an IAM user or role\. This means the permissions are limited to those granted to the IAM user or role\. For more information about how permissions for temporary security credentials are determined, see [Controlling Permissions for Temporary Security Credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_control-access.html) in the *IAM User Guide*\.
+ Verify that your requests are signed correctly and that the request is well\-formed\. For details, see the [toolkit](http://aws.amazon.com/tools/) documentation for your chosen SDK, or [Using Temporary Security Credentials to Request Access to AWS Resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html) in the *IAM User Guide*\.
+ Verify that your temporary security credentials haven't expired\. For more information, see [Requesting Temporary Security Credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_request.html) in the *IAM User Guide*\. 

## Changes I make aren't always immediately visible\.<a name="troubleshoot_general_eventual-consistency"></a>

As a service accessed through computers in data centers around the world, AWS Secrets Manager uses a distributed computing model called [eventual consistency](https://wikipedia.org/wiki/Eventual_consistency)\. Any change that you make in Secrets Manager \(or other AWS services\) takes time to become visible from all possible endpoints\. Some of the delay results from the time it takes to send the data from server to server, from replication zone to replication zone, and from region to region around the world\. Secrets Manager also uses caching to improve performance, but in some cases this can add time\. The change might not be visible until the previously cached data times out\.

Design your global applications to account for these potential delays\. Also, ensure that they work as expected, even when a change made in one location isn't instantly visible at another\.

For more information about how some other AWS services are affected by this, consult the following resources:
+ [Managing Data Consistency](https://docs.aws.amazon.com/redshift/latest/dg/managing-data-consistency.html) in the *Amazon Redshift Database Developer Guide*
+ [Amazon S3 Data Consistency Model](https://docs.aws.amazon.com/AmazonS3/latest/dev/Introduction.html#ConsistencyModel) in the *Amazon Simple Storage Service Developer Guide*
+ [Ensuring Consistency When Using Amazon S3 and Amazon Elastic MapReduce for ETL Workflows](http://aws.amazon.com/blogs/big-data/ensuring-consistency-when-using-amazon-s3-and-amazon-elastic-mapreduce-for-etl-workflows/) in the AWS Big Data Blog
+ [EC2 Eventual Consistency](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/query-api-troubleshooting.html#eventual-consistency) in the *Amazon EC2 API Reference*

## I receive a “cannot generate a data key with an asymmetric CMK” message when creating a secret\.<a name="asymmetrical-key"></a>

Verify you are using a symmetric customer master key \(CMK\) instead of an asymmetric CMK\. Secrets Manager uses a symmetric customer master key \(CMK\) associated with a secret to generate a data key for each secret value\. Secrets Manager also uses the CMK to decrypt that data key when it needs to decrypt the encrypted secret value\. You can track the requests and responses in AWS CloudTrail events, Amazon CloudWatch Logs, and audit trails\. You cannot use an asymmetric CMK at this time\. 