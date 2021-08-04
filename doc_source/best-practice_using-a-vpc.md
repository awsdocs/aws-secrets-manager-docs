# Running everything in a VPC<a name="best-practice_using-a-vpc"></a>

Whenever possible, you should run as much of your infrastructure on private networks not accessible from the public internet\. To do this, host your servers and services in a virtual private cloud \(VPC\) provided by Amazon VPC\. AWS provides a virtualized private network accessible only to the resources in your account\. The public internet can't view or access, unless you explicitly configure it with access\. For example, you could add a NAT gateway\. For complete information about Amazon VPC, see the [Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)\.

To enable secret rotation within a VPC environment, perform these steps:

1. Configure your Lambda rotation function to run within the same VPC as the database server or service with a rotated secret\. For more information, see [Configuring a Lambda Function to Access Resources in an Amazon VPC](https://docs.aws.amazon.com/lambda/latest/dg/vpc.html) in the *AWS Lambda Developer Guide*\.

1. The Lambda rotation function, now running from within your VPC, must be able to access a Secrets Manager service endpoint\. If the VPC has no direct Internet connectivity, then you can configure your VPC with a private Secrets Manager endpoint accessible by all of the resources in your VPC\. For details, see [VPC](rotating-secrets.md#rotation-network-rqmts)\.