# Using an AWS Secrets Manager VPC endpoint<a name="vpc-endpoint-overview"></a>

We recommend that you run as much of your infrastructure as possible on private networks that are not accessible from the public internet\. You can establish a private connection between your VPC and Secrets Manager by creating an *interface VPC endpoint*\. Interface endpoints are powered by [AWS PrivateLink](http://aws.amazon.com/privatelink), a technology that enables you to privately access Secrets Manager APIs without an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC don't need public IP addresses to communicate with Secrets Manager APIs\. Traffic between your VPC and Secrets Manager does not leave the AWS network\. For more information, see [Interface VPC endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html) in the *Amazon VPC User Guide*\. 

When Secrets Manager [rotates a secret by using a Lambda rotation function](rotating-secrets.md), for example a secret that contains database credentials, the Lambda function makes requests to both the database and Secrets Manager\. When you [turn on automatic rotation by using the console](rotate-secrets_turn-on-for-db.md), Secrets Manager creates the Lambda function in the same VPC as your database\. We recommend that you create a Secrets Manager endpoint in the same VPC so that requests from the Lambda rotation function to Secrets Manager don't leave the Amazon network\. 

If you enable private DNS for the endpoint, you can make API requests to Secrets Manager using its default DNS name for the Region, for example, `secretsmanager.us-east-1.amazonaws.com`\. For more information, see [Accessing a service through an interface endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#access-service-though-endpoint) in the *Amazon VPC User Guide*\.

You can make sure that requests to Secrets Manager come from the VPC access by including a condition in your permissions policies\. For more information, see [Example: Permissions and VPCs](auth-and-access_examples.md#auth-and-access_examples_vpc)\.

You can use AWS CloudTrail logs to audit your use of secrets through the VPC endpoint\. 

**To create a VPC endpoint for Secrets Manager**
+ For instructions, see [Creating an interface endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/create-interface-endpoint.html#create-interface-endpoint-aws) in the *Amazon VPC User Guide*\. Use the service name: com\.amazonaws\.*region*\.secretsmanager 

**To control access to the endpoint**
+ For instructions, see [Control access to VPC endpoints using endpoint policies](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints-access.html)\.  
**Example**  

  The following example policy grants access to all users and roles in account `123456789012`\.

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
**Example**  

  The following example policy restricts access to one specified secret\. 

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