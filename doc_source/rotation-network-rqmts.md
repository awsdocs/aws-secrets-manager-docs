# Network access for the rotation function<a name="rotation-network-rqmts"></a>

Secrets Manager uses a Lambda function to rotate a secret\. To be able to rotate a secret, the Lambda function must be able to access both the secret and the database or service:

**Access a secret**  
Your Lambda rotation function must be able to access a Secrets Manager endpoint\. If your Lambda function can access the internet, then you can use a public endpoint\. To find an endpoint, see [AWS Secrets Manager endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/asm.html)\.  
If your Lambda function runs in a VPC that doesn't have internet access, we recommend you configure Secrets Manager service private endpoints within your VPC\. Your VPC can then intercept requests addressed to the public regional endpoint and redirect them to the private endpoint\. For more information, see [VPC endpoint](vpc-endpoint-overview.md)\.  
Alternatively, you can enable your Lambda function to access a Secrets Manager public endpoint by adding a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) to your VPC, which allows traffic from your VPC to reach the public endpoint\. This exposes your VPC to more risk because an IP address for the gateway can be attacked from the public Internet\.

**Access the database or service**  
If your database or service is running on an Amazon EC2 instance in a VPC, we recommend that you configure your Lambda function to run in the same VPC\. Then the rotation function can communicate directly with your service\. For more information, see [Configuring VPC access](https://docs.aws.amazon.com/lambda/latest/dg/configuration-vpc.html#vpc-configuring)\.  
To allow the Lambda function to access the database or service, you must make sure that the security groups attached to your Lambda rotation function allow outbound connections to the database or service\. You must also make sure that the security groups attached to your database or service allow inbound connections from the Lambda rotation function\. For more information, see:  
+ Amazon RDS: [Controlling access with security groups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.RDSSecurityGroups.html)\. 
+ Amazon Redshift: [Managing VPC security groups for a cluster](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-vpc-security-groups.html)\.
+ Amazon DocumentDB: [Security Group Allows Inbound Connections](https://docs.aws.amazon.com/documentdb/latest/developerguide/troubleshooting.connecting.html#troubleshooting.cannot-connect.inbound-not-allowed)\.