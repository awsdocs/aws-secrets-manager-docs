# Creating and managing multi\-Region Secrets Manager secrets<a name="create-manage-multi-region-secrets"></a>

Secrets Manager lets you easily replicate your secrets in multiple AWS Regions to support applications spread across those Regions as well as disaster recovery scenarios\. You can create a primary secret in one AWS Region, and then replicate the secret to all AWS Regions where the application uses the secret\. Secrets Manager securely replicates the primary secret for use in specified AWS Regions without the overhead of managing a complex custom solution for this functionality\.

 Secrets Manager enables the easy lifecycle management of multi\-Region secrets by replicating the primary secret and the associated metadata to the replica secrets\. Replicated secrets have a common name across all Regions to enable you to easily find multi\-Region secrets and begin using them with minimal changes to your application\. Secrets Manager integrates with AWS Key Management Service \(AWS KMS\) to encrypt every version of every secret with a unique data key protected by a KMS key\. This integration protects your secrets under encryption keys that never leave AWS KMS unencrypted\. 

Secrets Manager replicates all encrypted secret data and metadata such as tags, resource policies and secret updates such as rotation across the specified Regions\. 

You can set up a single, tag\-based IAM policy to provision access to the secret in all Regions\. 

You can also use this feature to replicate secrets and read them in required Regions to meet Regional access and low latency requirements of your multi\-Region applications\. Use multi\-Region secrets to support your disaster recovery strategies by converting any secret replica to a stand\-alone secret and set it up for replication independently\.

You can replicate secrets across all of your enabled AWS Regions\. However, if you use Secrets Manager in special AWS Regions such as AWS GovCloud \(US\) or China Regions, you can only configure secrets and the replicas within these specialized AWS Regions\. You cannot replicate a secret in your enabled AWS Regions to a specialized Region or replicate secrets from a specialized region to a commercial region\. 

**Enabling Regions in AWS**  
An AWS Region is a collection of AWS resources in a geographic area\. Each AWS Region is isolated and independent of the other Regions\. Regions provide fault tolerance, stability, and resilience, and can also reduce latency\. They enable you to create redundant resources that remain available and unaffected by a Regional outage\.

If a Region is disabled by default, you must enable it before you can create and manage resources\. The following Regions are disabled by default:
+ Africa \(Cape Town\)
+ Asia Pacific \(Hong Kong\)
+ Europe \(Milan\)
+ Middle East \(Bahrain\)

When you enable a Region, AWS performs actions to prepare your account in that Region, such as distributing your IAM resources to the Region\. This process takes a few minutes for most accounts, but this can take several hours\. You cannot use the Region until this process is complete\.

For more information on enabling Regions, see [Managing AWS Regions](https://docs.aws.amazon.com/general/latest/gr/rande-manage.html)\.

You can manage multi\-Region secrets using the Secrets Manager console, API, or CLI or provision them using AWS CloudFormation templates\.

**Topics**
+ [Configuring primary and replica secrets](multi-region-config.md)
+ [Managing multi\-Region secrets in Secrets Manager](manage-multiregion-secret.md)