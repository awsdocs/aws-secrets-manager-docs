# AWS Secrets Manager quotas<a name="reference_limits"></a>

Secrets Manager read APIs have high TPS quotas, and control plane APIs that are less frequently called have lower TPS quotas\. We recommend you avoid calling `PutSecretValue` or `UpdateSecret` at a sustained rate of more than once every 10 minutes\. When you call `PutSecretValue` or `UpdateSecret` to update the secret value, Secrets Manager creates a new version of the secret\. Secrets Manager removes unlabeled versions when there are more than 100, but it does not remove versions created less than 24 hours ago\. If you update the secret value more than once every 10 minutes, you create more versions than Secrets Manager removes, and you will reach the quota for secret versions\.

You may operate multiple regions in your account, and each quota is specific to each region\.

When an application in one AWS account uses a secret owned by a different account, it's known as a *cross\-account request*\. For cross\-account requests, Secrets Manager throttles the account of the identity that makes the requests, not the account that owns the secret\. For example, if an identity from account A uses a secret in account B, the secret use applies only to the quotas in account A\.

## Secrets Manager quotas<a name="quotas"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_limits.html)

## Add retries to your application<a name="quotas_throttling"></a>

Your AWS client might see calls to Secrets Manager fail due to unexpected issues on the client side\. Or calls might fail due to rate limiting from Secrets Manager\. When you exceed an API request quota, Secrets Manager throttles the request\. It rejects an otherwise valid request and returns a throttling error\. For both kinds of failures, we recommend you retry the call after a brief waiting period\. This is called a [backoff and retry strategy](https://docs.aws.amazon.com/general/latest/gr/api-retries.html)\. 

If you experience the following errors, you might want to add retries to your application code:

**Transient errors and exceptions**
+ `RequestTimeout`
+ `RequestTimeoutException`
+ `PriorRequestNotComplete`
+ `ConnectionError`
+ `HTTPClientError`

**Service\-side throttling and limit errors and exceptions**
+ `Throttling`
+ `ThrottlingException`
+ `ThrottledException`
+ `RequestThrottledException`
+ `TooManyRequestsException`
+ `ProvisionedThroughputExceededException`
+ `TransactionInProgressException`
+ `RequestLimitExceeded`
+ `BandwidthLimitExceeded`
+ `LimitExceededException`
+ `RequestThrottled`
+ `SlowDown`

For more information, as well as example code, on retries, exponential backoff, and jitter, see the following resources:
+ [Exponential Backoff and Jitter](https://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/)
+ [Timeouts, retries and backoff with jitter](https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter)
+ [Error retries and exponential backoff in AWS](https://docs.aws.amazon.com/general/latest/gr/api-retries.html)\.