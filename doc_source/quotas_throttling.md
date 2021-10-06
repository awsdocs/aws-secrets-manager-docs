# Add retries to your application<a name="quotas_throttling"></a>

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