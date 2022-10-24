# Data protection in AWS Secrets Manager<a name="data-protection"></a>

The AWS [shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/) applies to data protection in AWS Secrets Manager\. As described in this model, AWS is responsible for protecting the global infrastructure that runs all of the AWS Cloud\. You are responsible for maintaining control over your content that is hosted on this infrastructure\. This content includes the security configuration and management tasks for the AWS services that you use\. For more information about data privacy, see the [Data Privacy FAQ](http://aws.amazon.com/compliance/data-privacy-faq)\. For information about data protection in Europe, see the [AWS Shared Responsibility Model and GDPR](http://aws.amazon.com/blogs/security/the-aws-shared-responsibility-model-and-gdpr/) blog post on the *AWS Security Blog*\.

For data protection purposes, we recommend that you protect AWS account credentials and set up individual user accounts with AWS Identity and Access Management \(IAM\)\. That way each user is given only the permissions necessary to fulfill their job duties\. We also recommend that you secure your data in the following ways:
+ Use multi\-factor authentication \(MFA\) with each account\.
+ Use SSL/TLS to communicate with AWS resources\. We recommend TLS 1\.2 or later\.
+ Set up API and user activity logging with AWS CloudTrail\.
+ Use AWS encryption solutions, along with all default security controls within AWS services\.
+ Use advanced managed security services such as Amazon Macie, which assists in discovering and securing personal data that is stored in Amazon S3\.
+ If you require FIPS 140\-2 validated cryptographic modules when accessing AWS through a command line interface or an API, use a FIPS endpoint\. For more information about the available FIPS endpoints, see [Federal Information Processing Standard \(FIPS\) 140\-2](http://aws.amazon.com/compliance/fips/)\.

We strongly recommend that you never put confidential or sensitive information, such as your customers' email addresses, into tags or free\-form fields such as a **Name** field\. This includes when you work with Secrets Manager or other AWS services using the console, API, AWS CLI, or AWS SDKs\. Any data that you enter into tags or free\-form fields used for names may be used for billing or diagnostic logs\. If you provide a URL to an external server, we strongly recommend that you do not include credentials information in the URL to validate your request to that server\.

## Encryption at rest<a name="encryption-at-rest"></a>

Secrets Manager uses encryption via AWS Key Management Service \(AWS KMS\) to protect the confidentiality of data at rest\. AWS KMS provides a key storage and encryption service used by many AWS services\. Every secret in Secrets Manager is encrypted with a unique data key\. Each data key is protected by a KMS key\. You can choose to use default encryption with the Secrets Manager AWS managed key for the account, or you can create your own customer managed key in AWS KMS\. Using a customer managed key gives you more granular authorization controls over your KMS key activities\. For more information, see [Secret encryption and decryption in AWS Secrets Manager](security-encryption.md)\. 

## Encryption in transit<a name="encryption-in-transit"></a>

Secrets Manager provides secure and private endpoints for encrypting data in transit\. The secure and private endpoints allows AWS to protect the integrity of API requests to Secrets Manager \. AWS requires API calls be signed by the caller using X\.509 certificates and/or a Secrets Manager Secret Access Key\. This requirement is stated in the [Signature Version 4 Signing Process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) \(Sigv4\)\. 

If you use the AWS Command Line Interface \(AWS CLI\) or any of the AWS SDKs to make calls to AWS, you configure the access key to use\. Then those tools automatically use the access key to sign the requests for you\. 

### Post\-quantum TLS<a name="pqtls"></a>

Secrets Manager supports a hybrid post\-quantum key exchange option for the Transport Layer Security \(TLS\) network encryption protocol\. You can use this TLS option when you connect to Secrets Manager API endpoints\. We're offering this feature before post\-quantum algorithms are standardized so you can begin testing the effect of these key exchange protocols on Secrets Manager calls\. These optional hybrid post\-quantum key exchange features are at least as secure as the TLS encryption we use today and are likely to provide additional security benefits\. However, they affect latency and throughput compared to the classic key exchange protocols in use today\.

To protect data encrypted today against potential future attacks, AWS is participating with the cryptographic community in the development of quantum\-resistant or *post\-quantum* algorithms\. We've implemented hybrid post\-quantum key exchange cipher suites in Secrets Manager endpoints\. These hybrid cipher suites, which combine classic and post\-quantum elements, ensure that your TLS connection is at least as strong as it would be with classic cipher suites\. However, because the performance characteristics and bandwidth requirements of hybrid cipher suites are different from those of classic key exchange mechanisms, we recommend that you test them on your API calls\. 

For more information about using hybrid post\-quantum TLS, see:
+ [Introducing AWS Common Runtime HTTP Client in the AWS SDK for Java 2\.x](http://aws.amazon.com/blogs/developer/introducing-aws-common-runtime-http-client-in-the-aws-sdk-for-java-2-x/) 
+ [AWS SDK for Java 2\.x Developer Guide](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/) and the [AWS SDK for Java 2\.x released](http://aws.amazon.com/blogs/developer/aws-sdk-for-java-2-x-released/) blog post\.
+ [Introducing s2n\-tls, a New Open Source TLS Implementation](http://aws.amazon.com/blogs/security/introducing-s2n-a-new-open-source-tls-implementation/) and [Using s2n\-tls](https://github.com/aws/s2n-tls/blob/main/docs/USAGE-GUIDE.md)\.
+ [Post\-Quantum Cryptography](https://csrc.nist.gov/Projects/Post-Quantum-Cryptography) at the National Institute for Standards and Technology \(NIST\)\.
+ [Hybrid Post\-Quantum Key Encapsulation Methods \(PQ KEM\) for Transport Layer Security 1\.2 \(TLS\)](https://tools.ietf.org/html/draft-campagna-tls-bike-sike-hybrid-01)\.

Post\-quantum TLS for Secrets Manager is available in all AWS Regions except for AWS GovCloud \(US\) and China\. 

## Encryption key management<a name="encryption-key-management"></a>

When Secrets Manager needs to encrypt a new version of the protected secret data, Secrets Manager sends a request to AWS KMS to generate a new data key from the KMS key\. Secrets Manager uses this data key for [envelope encryption](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping)\. Secrets Manager stores the encrypted data key with the encrypted secret\. When the secret needs to be decrypted, Secrets Manager asks AWS KMS to decrypt the data key\. Secrets Manager then uses the decrypted data key to decrypt the encrypted secret\. Secrets Manager never stores the data key in unencrypted form and removes the key from memory as soon as possible\. For more information, see [Secret encryption and decryption in AWS Secrets Manager](security-encryption.md)\.

## Inter\-network traffic privacy<a name="inter-network-traffic-privacy"></a>

AWS offers options for maintaining privacy when routing traffic through known and private network routes\. 

### Traffic between service and on\-premises clients and applications<a name="btw-service-and-on-prem"></a>

You have two connectivity options between your private network and AWS Secrets Manager: 
+ An AWS Site\-to\-Site VPN connection\. For more information, see [What is AWS Site\-to\-Site VPN?](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html)
+ An AWS Direct Connect connection\. For more information, see [What is AWS Direct Connect?](https://docs.aws.amazon.com/directconnect/latest/UserGuide/Welcome.html)

### Traffic between AWS resources in the same Region<a name="traffic-in-same-region"></a>

If you want to secure traffic between Secrets Manager and API clients in AWS, set up an [AWS PrivateLink](https://aws.amazon.com/privatelink/) to privately access Secrets Manager API endpoints\. 