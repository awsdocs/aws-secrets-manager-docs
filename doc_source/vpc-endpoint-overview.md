# Using Secrets Manager with VPC Endpoints<a name="vpc-endpoint-overview"></a>

 The following sections explain these tasks:
+ Connecting Secrets Manager and a VPC endpoint\.
+ Creating a Secrets Manager VPC endpoint\.
+ Creating an endpoint policy for your Secrets Manager endpoint,

For information about VPC endpoints, see the article in the VPC service documentation, [VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html)\.

**Topics**
+ [Connecting to Secrets Manager Through a VPC Endpoint](#vpc-endpoint)
+ [Create a Secrets Manager VPC Private Endpoint](#vpc-endpoint-create)
+ [Connecting to a Secrets Manager VPC Private Endpoint](#vpc-endpoint-connect)
+ [Using a VPC Private Endpoint in a Policy Statement](#vpc-endpoint-policies)
+ [Create an Endpoint Policy for Your Secrets Manager VPC Endpoint](#vpc-endpoint-policy)
+ [Audit the Use of Your Secrets Manager VPC Endpoint](#vpc-endpoint-audit)

## Connecting to Secrets Manager Through a VPC Endpoint<a name="vpc-endpoint"></a>

Instead of connecting your VPC to an internet, you can connect directly to Secrets Manager through a private endpoint you configure within your VPC\. When you use a VPC service endpoint, communication between your VPC and Secrets Manager occurs entirely within the AWS network, and requires no public Internet access\.

Secrets Manager supports Amazon VPC [interface endpoints](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpce-interface.html) provided by [AWS PrivateLink](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Introduction.html#what-is-privatelink)\. One or more Each VPC endpoint is represented by one or more [elastic network interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html) with private IP addresses in your VPC subnets\.

The VPC interface endpoint connects your VPC directly to Secrets Manager without a NAT device, VPN connection, or AWS Direct Connect connection\. The instances in your VPC don't require public IP addresses to communicate with Secrets Manager\.

For your Lambda rotation function to find the private endpoint, perform one of the following steps:
+ You can manually specify the VPC endpoint in [Secrets Manager API operations](https://docs.aws.amazon.com/secretsmanager/latest/apireference/) and [AWS CLI commands](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/index.html)\. For example, the following command uses the **endpoint\-url** parameter to specify a VPC endpoint in an AWS CLI command to Secrets Manager\.

  ```
  $ aws secretsmanager list-secrets --endpoint-url https://vpce-1234a5678b9012c-12345678.secretsmanager.us-west-2.vpce.amazonaws.com
  ```
+ If you enable [private DNS hostnames](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#vpce-private-dns) for your VPC private endpoint, you don't need to specify the endpoint URL\. The standard Secrets Manager DNS hostname the Secrets Manager CLI and SDKs use by default \(`https://secretsmanager.<region>.amazonaws.com`\) automatically resolves to your VPC endpoint\.

You can also use AWS CloudTrail logs to audit your use of secrets through the VPC endpoint\. And you can use the conditions in IAM and secret resource\-based policies to deny access to any request that doesn't originate from a specified VPC or VPC endpoint\.

**Note**  
Use caution when creating IAM and key policies based on your VPC endpoint\. If a policy statement requires the requests originate from a particular VPC or VPC endpoint, then requests from other AWS services interacting with the secret on your behalf might fail\. For help, see [Using VPC Endpoint Conditions in Policies with Secrets Manager Permissions](reference_iam-permissions.md#iam-contextkeys-vpcendpoint)\.

**Regions**

Secrets Manager supports VPC endpoints in all AWS Regions where both [Amazon VPC](https://docs.aws.amazon.com/general/latest/gr/rande.html#vpc_region) and [Secrets Manager](https://docs.aws.amazon.com/general/latest/gr/rande.html#asm_region) are available\.

## Create a Secrets Manager VPC Private Endpoint<a name="vpc-endpoint-create"></a><a name="proc-vpc-endpoint-create"></a>

**To create a Secrets Manager VPC private endpoint**  
Follow the steps under one of the following tabs:

------
#### [ Using the AWS Management Console ]<a name="proc-vpc-endpoint-create-console"></a>

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. On the navigation bar, use the region selector to choose the region\.

1. In the navigation pane, choose **Endpoints**\. In the main pane, choose **Create Endpoint**\.

1. For **Service category**, choose **AWS services**\.

1. In the **Service Name** list, choose the entry for the Secrets Manager interface endpoint in the region\. For example, in the US East \(N\.Virginia\) Region, the entry name is `com.amazonaws.us-east-1.secretsmanager`\.

1. For **VPC**, choose your VPC\.

1. For **Subnets**, choose a subnet from each Availability Zone to include\.

   The VPC endpoint can span multiple Availability Zones\. AWS creates an elastic network interface for the VPC endpoint in each subnet that you choose\. Each network interface has a DNS hostname and a private IP address\.

1. By default, AWS enables the **Enable Private DNS Name** option, the standard Secrets Manager DNS hostname \(`https://secretsmanager.<region>.amazonaws.com`\) automatically resolves to your VPC endpoint\. This option makes it easier to use the VPC endpoint\. The Secrets Manager CLI and SDKs use the standard DNS hostname by default, so you don't need to specify the VPC endpoint URL in applications and commands\.

   This feature works only when you set the `enableDnsHostnames` and `enableDnsSupport` attributes of your VPC to `true` , the default values\. To set these attributes, [update DNS support for your VPC](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-dns.html#vpc-dns-updating)\.

1. For **Security group**, select or create a security group\.

   You can use [security groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) to control access to your endpoint, much like you would use a firewall\.

1. Choose **Create endpoint**\.

The results display the VPC endpoint, including the VPC endpoint ID and the DNS names you use to [connect to your VPC endpoint](https://docs.aws.amazon.com/kms/latest/developerguide/kms-vpc-endpoint.html#connecting-vpc-endpoint)\.

You can also use the Amazon VPC tools to view and manage your endpoint\. This includes creating a notification for an endpoint, changing properties of the endpoint, and deleting the endpoint\. For instructions, see [Interface VPC Endpoints](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpce-interface.html)\.

------
#### [ Using the AWS CLI or SDK Operations ]

You can use the [create\-vpc\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc-endpoint.html) command in the AWS CLI to create a VPC endpoint that connects to Secrets Manager\.

Be sure to use `interface` as the VPC endpoint type\. Also, use a service name value that includes `secretsmanager` and the region where you located your VPC\.

The command doesn't include the `PrivateDnsNames` parameter because the VPC defaults to the value `true`\. To disable the option, you can include the parameter with a value of `false`\. Private DNS names are available only when the `enableDnsHostnames` and `enableDnsSupport` attributes of your VPC are set to `true`\. To set these attributes, use the [ModifyVpcAttribute](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyVpcAttribute.html) API\.

The following diagram shows the general syntax of the command\.

```
aws ec2 create-vpc-endpoint  --vpc-id <vpc id> \
                             --vpc-endpoint-type Interface \
                             --service-name com.amazonaws.<region>.secretsmanager \
                             --subnet-ids <subnet id> \
                             --security-group-id <security group id>
```

For example, the following command creates a VPC endpoint in the VPC with VPC ID `vpc-1a2b3c4d`, which is in the `us-west-2` Region\. It specifies just one subnet ID to represent the Availability Zones, but you can specify many\. VPC endpoints require the security group ID\.

The output includes the VPC endpoint ID and DNS names you can use to connect to your new VPC endpoint\.

```
$ aws ec2 create-vpc-endpoint  --vpc-id vpc-1a2b3c4d \
                               --vpc-endpoint-type Interface \
                               --service-name com.amazonaws.us-west-2.secretsmanager \
                               --subnet-ids subnet-e5f6a7b8c9 \
                               --security-group-id sg-1a2b3c4d
{
  "VpcEndpoint": {
      "PolicyDocument": "{\n  \"Statement\": [\n    {\n      \"Action\": \"*\", \n      \"Effect\": \"Allow\", \n      \"Principal\": \"*\", \n      \"Resource\": \"*\"\n    }\n  ]\n}",
      "VpcId": "vpc-1a2b3c4d",
      "NetworkInterfaceIds": [
          "eni-abcdef12"
      ],
      "SubnetIds": [
          "subnet-e5f6a7b8c9"
      ],
      "PrivateDnsEnabled": true,
      "State": "pending",
      "ServiceName": "com.amazonaws.us-west-2.secretsmanager",
      "RouteTableIds": [],
      "Groups": [
          {
              "GroupName": "default",
              "GroupId": "sg-1a2b3c4d"
          }
      ],
      "VpcEndpointId": "vpce-1234a5678b9012c",
      "VpcEndpointType": "Interface",
      "CreationTimestamp": "2018-06-12T20:14:41.240Z",
      "DnsEntries": [
          {
              "HostedZoneId": "Z7HUB22UULQXV",
              "DnsName": "vpce-1234a5678b9012c-12345678.secretsmanager.us-west-2.vpce.amazonaws.com"
          },
          {
              "HostedZoneId": "Z7HUB22UULQXV",
              "DnsName": "vpce-1234a5678b9012c-12345678-us-west-2a.secretsmanager.us-west-2.vpce.amazonaws.com"
          },
          {
              "HostedZoneId": "Z1K56Z6FNPJRR",
              "DnsName": "secretsmanager.us-west-2.amazonaws.com"
          }
      ]
  }
}
```

------

## Connecting to a Secrets Manager VPC Private Endpoint<a name="vpc-endpoint-connect"></a>

Because, by default, VPC automatically enables private DNS names when you create a VPC private endpoint, you don't need to do anything other than use the standard endpoint DNS name for your region\. The endpoint DNS name automatically resolves to the correct endpoint within your VPC:

```
https://secretsmanager.<region>.amazonaws.com
```

The AWS CLI and SDKs use this hostname by default, so you can begin using the VPC endpoint without changing anything in your scripts and application\.

If you don't enable private DNS names, you can still connect to the endpoint by using the full DNS name\.

For example, this [list\-secrets](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/list-secrets.html) command uses the `endpoint-url` parameter to specify the VPC private endpoint\. To use a command like this, replace the example VPC private endpoint ID with one in your account\.

```
aws secretsmanager list-secrets --endpoint-url https://vpce-1234a5678b9012c-12345678.secretsmanager.us-west-2.vpce.amazonaws.com
```

## Using a VPC Private Endpoint in a Policy Statement<a name="vpc-endpoint-policies"></a>

You can use IAM policies and Secrets Manager secret policies to control access to your secrets\. You can also use [global condition keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#AvailableKeys) to restrict these policies based on the VPC endpoint or VPC in the request\.
+ Use the `aws:sourceVpce` condition key to grant or restrict access to a secret based on the VPC endpoint\.
+ Use the `aws:sourceVpc` condition key to grant or restrict access to a secret based on the VPC that hosts the private endpoint\.

**Note**  
Use caution when you create IAM and secret policies based on your VPC endpoint\. If a policy statement requires the requests to originate from a particular VPC or VPC endpoint, then requests from other AWS services accessing the secret on your behalf might fail\. For more information, see [Using VPC Endpoint Conditions in Policies with Secrets Manager Permissions](reference_iam-permissions.md#iam-contextkeys-vpcendpoint)\.  
Also, the `aws:sourceIP` condition key isn't effective when the request comes from an Amazon VPC endpoint\. To restrict requests to a VPC endpoint, use the `aws:sourceVpce` or `aws:sourceVpc` condition keys\. For more information, see [VPC Endpoints \- Controlling the Use of Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html#vpc-endpoints-iam-access) in the *Amazon VPC User Guide*\.

For example, the following sample secret policy allows a user to perform Secrets Manager operations only when the request comes through the specified VPC endpoint\.

When a user sends a request to Secrets Manager, Secrets Manager compares the VPC endpoint ID in the request to the `aws:sourceVpce` condition key value in the policy\. If they don't match, Secrets Manager denies the request\.

To use a policy like this one, replace the placeholder AWS account ID and VPC endpoint IDs with valid values for your account\.

```
{
    "Id": "example-policy-1",
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "EnableSecretsManagerpermissions",
            "Effect": "Allow",
            "Principal": {"AWS":["123456789012"]},
            "Action": ["secretsmanager:*"],
            "Resource": "*"
        },
        {
            "Sid": "RestrictGetSecretValueoperation",
            "Effect": "Deny",
            "Principal": "*",
            "Action": ["secretsmanager:GetSecretValue"],
            "Resource": "*",
            "Condition": {
                "StringNotEquals": {
                    "aws:sourceVpce": "vpce-1234a5678b9012c"
                }
            }
        }

    ]
}
```

You can also use the `aws:sourceVpc` condition key to restrict access to your secrets based on the VPC where the VPC endpoint resides\.

The following sample secret policy allows commands to create and manage secrets only when they come from `vpc-12345678`\. In addition, the policy allows operations that use access the secret encrypted value only when the requests come from `vpc-2b2b2b2b`\. You might use a policy like this one if you run an application in one VPC, but you use a second, isolated VPC for management functions\.

To use a policy like this one, replace the placeholder AWS account ID and VPC endpoint IDs with valid values for your account\.

```
{
    "Id": "example-policy-2",
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowAdministrativeActionsfromONLYvpc-12345678",
            "Effect": "Allow",
            "Principal": {"AWS": "123456789012"},
            "Action": [
                "secretsmanager:Create*",
                "secretsmanager:Put*",
                "secretsmanager:Update*",
                "secretsmanager:Delete*","secretsmanager:Restore*",
                "secretsmanager:RotateSecret","secretsmanager:CancelRotate*",
                "secretsmanager:TagResource","secretsmanager:UntagResource"
            ],
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "aws:sourceVpc": "vpc-12345678"
                }
            }
        },
        {
            "Sid": "AllowSecretValueAccessfromONLYvpc-2b2b2b2b",
            "Effect": "Allow",
            "Principal": {"AWS": "111122223333"},
            "Action": ["secretsmanager:GetSecretValue"],
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "aws:sourceVpc": "vpc-2b2b2b2b"
                }
            }
        },
        {
            "Sid": "AllowReadActionsEverywhere",
            "Effect": "Allow",
            "Principal": {"AWS": "111122223333"},
            "Action": [
                "secretsmanager:Describe*","secretsmanager:List*","kms:GetRandomPassword"
            ],
            "Resource": "*",
        }
    ]
}
```

## Create an Endpoint Policy for Your Secrets Manager VPC Endpoint<a name="vpc-endpoint-policy"></a>

Once you create a Secrets Manager VPC endpoint, you can attach an endpoint policy to control secrets\-related activity on the endpoint\. For example, you can attach an endpoint policy to define the performed Secrets Manager actions, actions performed on the secrets, the IAM users or roles performing these actions, and the accounts accessed through the VPC endpoint\. For additional information about endpoint policies, including a list of the AWS services supporting endpoint policies, see [Using VPC Endpoint policies](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-access.html#vpc-endpoints-policies)\. 

**Note**  
AWS does not share VPC endpoints across AWS services\. If you use VPC endpoints for multiple AWS services, such as Secrets Manager and S3, you must attach a distinct policy to each endpoint\.

 **Example: Enable access to the Secrets Manager endpoint for a specific account** 

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

 **Example: Enable access to a single secret on the Secrets Manager endpoint** 

The following example restricts access to only the specified secret\. 

```
      {
         "Statement": [
            {
              "Principal": "*",
              "Action": "secretsmanager:*",
              "Effect": "Allow",
              "Resource": [
                      "arn:aws:secretsmanager:us-west:123456789012:secret:a_specific_secret_name-a1b2c3"
                      ]
            }
          ]
      }
```

## Audit the Use of Your Secrets Manager VPC Endpoint<a name="vpc-endpoint-audit"></a>

When a request to Secrets Manager uses a VPC endpoint, the VPC endpoint ID appears in the [AWS CloudTrail log](monitoring.md) entry that records the request\. You can use the endpoint ID to audit the use of your Secrets Manager VPC endpoint\.

For example, this sample log entry records a `GenerateDataKey` request that used the VPC endpoint\. In this example, the `vpcEndpointId` field appears at the end of the log entry\. For brevity, many irrelevant parts of the example have been omitted\.

```
      {
	"eventVersion":"1.05",
	"userIdentity": 
	     {
             "type": "IAMUser",
             "arn": "arn:aws:iam::123456789012:user/Anika",
             "accountId": "123456789012",
             "userName": "Anika"
            },
	"eventTime":"2018-01-16T05:46:57Z",
	"eventSource":"secretsmanager.amazonaws.com",
	"eventName":"GetSecretValue",
	"awsRegion":"us-west-2",
	"sourceIPAddress":"172.01.01.001",
	"userAgent":"aws-cli/1.14.23 Python/2.7.12 Linux/4.9.75-25.55.amzn1.x86_64 botocore/1.8.27",
	"requestID":"EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE",
	"eventID":"EXAMPLE2-90ab-cdef-fedc-ba987EXAMPLE",
	"readOnly":true,
	"eventType":"AwsApiCall",
	"recipientAccountId":"123456789012",
	"vpcEndpointId": "vpce-1234a5678b9012c"
      }
```