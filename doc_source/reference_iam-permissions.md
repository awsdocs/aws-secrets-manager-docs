# Permissions reference for Secrets Manager<a name="reference_iam-permissions"></a>

To see the elements that make up a permissions policy, see [JSON policy document structure](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html#policies-introduction) and [IAM JSON policy elements reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html)\. 

To get started writing your own permissions policy, see [Permissions policy examples](auth-and-access_examples.md)\.

## Secrets Manager actions<a name="reference_iam-permissions_actions"></a>

See [Actions defined by AWS Secrets Manager](https://docs.aws.amazon.com/service-authorization/latest/reference/list_awssecretsmanager.html#awssecretsmanager-actions-as-permissions)\. 

## Secrets Manager resources<a name="iam-resources"></a>

See [Resource types defined by AWS Secrets Manager](https://docs.aws.amazon.com/service-authorization/latest/reference/list_awssecretsmanager.html#awssecretsmanager-resources-for-iam-policies)\.

Secrets Manager constructs the last part of the secret ARN by appending a dash and six random alphanumeric characters at the end of the secret name\. If you delete a secret and then recreate another with the same name, this formatting helps ensure that individuals with permissions to the original secret don't automatically get access to the new secret because Secrets Manager generates six new random characters\.

You can find the ARN for a secret in the Secrets Manager console on the secret details page or by calling [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DescribeSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DescribeSecret.html)\.

## Condition keys<a name="iam-contextkeys"></a>

See [Condition keys for AWS Secrets Manager](https://docs.aws.amazon.com/service-authorization/latest/reference/list_awssecretsmanager.html#awssecretsmanager-policy-keys)\.

## IP address conditions<a name="iam-contextkeys-ipaddress"></a>

Use caution when you specify the [IP address condition operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html#Conditions_IPAddress) or the `aws:SourceIp` condition key in a policy statement that allows or denies access to Secrets Manager\. For example, if you attach a policy that restricts AWS actions to requests from your corporate network IP address range to a secret, then your requests as an IAM user invoking the request from the corporate network work as expected\. However, if you enable other services to access the secret on your behalf, such as when you enable rotation with a Lambda function, that function calls the Secrets Manager operations from an AWS\-internal address space\. Requests impacted by the policy with the IP address filter fail\.

Also, the `aws:sourceIP` condition key is less effective when the request comes from an Amazon VPC endpoint\. To restrict requests to a specific VPC endpoint, use [VPC endpoint conditions](#iam-contextkeys-vpcendpoint)\.

## VPC endpoint conditions<a name="iam-contextkeys-vpcendpoint"></a>

To allow or deny access to requests from a particular VPC or VPC endpoint, use `aws:SourceVpc` to limit access to requests from the specified VPC or `aws:SourceVpce` to limit access to requests from the specified VPC endpoint\. See [Example: Permissions and VPCs](auth-and-access_examples.md#auth-and-access_examples_vpc)\.
+ `aws:SourceVpc` limits access to requests from the specified VPC\.
+ `aws:SourceVpce` limits access to requests from the specified VPC endpoint\.

If you use these condition keys in a resource policy statement that allows or denies access to Secrets Manager secrets, you can inadvertently deny access to services that use Secrets Manager to access secrets on your behalf\. Only some AWS services can run with an endpoint within your VPC\. If you restrict requests for a secret to a VPC or VPC endpoint, then calls to Secrets Manager from a service not configured for the service can fail\.

See [Using AWS Secrets Manager with VPC endpoints](vpc-endpoint-overview.md)\.