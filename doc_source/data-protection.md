# Data Protection in AWS Secrets Manager<a name="data-protection"></a>

AWS Secrets Manager conforms to the AWS [shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/), which includes regulations and guidelines for data protection\. AWS is responsible for protecting the global infrastructure that runs all the AWS services\. AWS maintains control over data hosted on this infrastructure, including the security configuration controls for handling customer content and personal data\. AWS customers and APN partners, acting either as data controllers or data processors, are responsible for any personal data that they put in the AWS Cloud\. 

For data protection purposes, we recommend you protect AWS account credentials and set up individual user accounts with AWS Identity and Access Management \(IAM\), so that each user receives only the permissions necessary to fulfill their job duties\. 

## Encryption at Rest<a name="encryption-at-rest"></a>

Secrets Manager uses encryption via AWS Key Management Service \(KMS\) to protect the confidentiality of data at rest\. AWS KMS provides a key storage and encryption service used by many AWS services\. AWS Secrets Manager associates every secret with an AWS KMS [customer master key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#master_keys) \(CMK\)\. The associated CMK can either be the default Secrets Manager CMK for the account, or you can create your own CMK\. For additional information, on creating CMKs, see [How Secrets Manager Uses AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/services-secrets-manager.html)\. 

## Encryption in Transit<a name="encryption-in-transit"></a>

Secrets Manager provides secure and private endpoints for encrypting data in transit\. The secure and private endpoints allows AWS to protect the integrity of API requests to Secrets Manager \. AWS requires API calls be signed by the caller using X\.509 certificates and/or a AWS Secrets Manager Secret Access Key\. This requirement is stated in the [Signature Version 4 Signing Process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) \(Sigv4\)\. 

 If you use the AWS Command Line Interface \(CLI\) or any of the AWS SDKs to make calls to AWS, those tools automatically use the specified access key to sign the requests for you\. When you configure the CLI or any of the AWS SDKs, you specify the access key to use\.

## Encryption Key Management<a name="encryption-key-management"></a>

When Secrets Manager needs to encrypt a new version of the protected secret data, Secrets Manager sends a request to AWS KMS to generate a new data key from the specified CMK\. Secrets Manager uses this data key for [envelope encryption](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping)\. Secrets Manager stores the encrypted data key with the encrypted secret\. When the secret needs to be decrypted, Secrets Manager asks AWS KMS to decrypt the data key\. Secrets Manager then uses the decrypted data key to decrypt the encrypted secret\. Secrets Manager never stores the data key in unencrypted form, and removes the key from memory as soon as possible\.

For additional information on the encryption and decryption process, as well as a complete explanation of how Secrets Manager uses AWS KMS, see [How AWS Secrets Manager Uses AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/services-secrets-manager.html)\.

## Inter\-network traffic privacy<a name="inter-network-traffic-privacy"></a>

AWS offers options for maintaining privacy when routing traffic through known and private network routes\. 

### Traffic Between Service and On\-Premises Clients and Applications<a name="btw-service-and-on-prem"></a>

You have two connectivity options between your private network and AWS Secrets Manager: 
+ An AWS Site\-to\-Site VPN connection\. For more information, see [What is AWS Site\-to\-Site VPN?](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html)
+ An AWS Direct Connect connection\. For more information, see [What is AWS Direct Connect?](https://docs.aws.amazon.com/directconnect/latest/UserGuide/Welcome.html)

### Traffic Between AWS Resources in the Same Region<a name="traffic-in-same-region"></a>

If want to secure traffic between Secrets Manager and API clients in AWS, set up an [AWS PrivateLink](https://aws.amazon.com/privatelink/) to privately access Secrets Manager API endpoints\. 