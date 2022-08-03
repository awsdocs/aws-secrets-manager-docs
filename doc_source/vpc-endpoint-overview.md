# Using an AWS Secrets Manager VPC endpoint<a name="vpc-endpoint-overview"></a>

We recommend that you run as much of your infrastructure as possible on private networks that are not accessible from the public internet\. You can establish a private connection between your VPC and Secrets Manager by creating an *interface VPC endpoint*\. Interface endpoints are powered by [AWS PrivateLink](http://aws.amazon.com/privatelink), a technology that enables you to privately access Secrets Manager APIs without an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC don't need public IP addresses to communicate with Secrets Manager APIs\. Traffic between your VPC and Secrets Manager does not leave the AWS network\. 

Each interface endpoint is represented by one or more [Elastic Network Interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html) in your subnets\. 

For more information, see [Interface VPC endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html) in the *Amazon VPC User Guide*\. 

## Considerations for Secrets Manager VPC endpoints<a name="vpc-endpoint-considerations"></a>

Before you set up an interface VPC endpoint for Secrets Manager, ensure that you review [Interface endpoint properties and limitations](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#vpce-interface-limitations) in the *Amazon VPC User Guide*\. 

Automatic secret rotation uses a Lamba function, and the Lambda function makes requests to both the database and Secrets Manager\. When you turn on automatic rotation, Secrets Manager creates the Lambda function in the same VPC as your database\. We recommend you create a Secrets Manager endpoint in the same VPC so that requests from the Lambda rotation function to Secrets Manager don't leave the Amazon network\. For more information, see [Network access for the rotation function](rotation-network-rqmts.md)\.

Secrets Manager supports making calls to all of its API actions from your VPC\. 

You can use AWS CloudTrail logs to audit your use of secrets through the VPC endpoint\. 

For information about denying access to requests that don't originate from a specified VPC or VPC endpoint, see [Example: Permissions and VPCs](auth-and-access_examples.md#auth-and-access_examples_vpc)\. 

## Creating an interface VPC endpoint for Secrets Manager<a name="vpc-endpoint-create"></a>

You can create a VPC endpoint for the Secrets Manager service using either the Amazon VPC console or the AWS Command Line Interface \(AWS CLI\)\. For more information, see [Creating an interface endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#create-interface-endpoint) in the *Amazon VPC User Guide*\.

Create a VPC endpoint for Secrets Manager using the following service name: 
+ com\.amazonaws\.*region*\.secretsmanager 

If you enable private DNS for the endpoint, you can make API requests to Secrets Manager using its default DNS name for the Region, for example, `secretsmanager.us-east-1.amazonaws.com`\. 

For more information, see [Accessing a service through an interface endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#access-service-though-endpoint) in the *Amazon VPC User Guide*\.

## Creating a VPC endpoint policy for Secrets Manager<a name="vpc-endpoint-policy"></a>

You can attach an endpoint policy to your VPC endpoint that controls access to Secrets Manager\. The policy specifies the following information:
+ The principal that can perform actions\.
+ The actions that can be performed\.
+ The resources on which actions can be performed\.

For more information, see [Controlling access to services with VPC endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-access.html) in the *Amazon VPC User Guide*\. 

**Example Enable access to the Secrets Manager endpoint for a specific account**  
The following example grants access to all users and roles in account `123456789012`\.  

```
{
  "Statement": [
    {
      "Sid": "AccessSpecificAccount",
      "Principal": {"AWS": "123456789012"},
      "Action": "secretsmanager:*",
      "Effect": "Allow",
      "Resource": "*"
    }
  ]
}
```

**Example Enable access to a single secret on the Secrets Manager endpoint**  
The following example restricts access to only the specified secret\.   

```
{
  "Statement": [
    {
      "Principal": "*",
      "Action": "secretsmanager:*",
      "Effect": "Allow",
      "Resource": [
        "arn:aws:secretsmanager:us-east-2:111122223333:secret:SecretName-a1b2c3"
      ]
    }
  ]
}
```