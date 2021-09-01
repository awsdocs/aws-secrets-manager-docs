# Adding retries to your application<a name="quotas_throttling"></a>

Your AWS client might see calls to Secrets Manager fail due to unexpected issues on the client side\. Or calls might fail due to rate limiting from the Secrets Manager resource you attempt to invoke\. When you exceed an API request quota, Secrets Manager throttles the request, that is, it rejects an otherwise valid request and returns a `ThrottlingException` error\. To respond, use a [backoff and retry strategy](https://docs.aws.amazon.com/general/latest/gr/api-retries.html)\.In either case, these kinds of failures often don’t require special handling and the call should be made again, often after a brief waiting period\. AWS provides many features to assist in retrying client calls to AWS services when you experience these kinds of errors or exceptions\.

If you experience errors such as the following, you might want to add `retries` to your application code:

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
+ [Timeouts,retries and backoff with jitter](https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter)
+ [Error retries and exponential backoff in AWS](https://docs.aws.amazon.com/general/latest/gr/api-retries.html)\.
+ [KMS: Request Quotas](https://docs.aws.amazon.com/kms/latest/developerguide/requests-per-second.html)
+ [AWS SDK for Python \(Boto3\)](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/retries.html)
+ [AWS Tools and SDKs Shared Configuration and Credentials Reference Guide](https://docs.aws.amazon.com/credref/latest/refdocs/setting-global-retry_mode.html)
+ [AWS SDK for Java](https://sdk.amazonaws.com/java/api/latest/)
+ [AWS SDK for Ruby](https://docs.aws.amazon.com/sdk-for-ruby/v3/developer-guide/timeout-duration.html)
+ [AWS SDK for Go API Reference](https://docs.aws.amazon.com/sdk-for-go/api/service/secretsmanager/)