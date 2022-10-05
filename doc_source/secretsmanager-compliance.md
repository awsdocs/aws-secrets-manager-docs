# Compliance validation for AWS Secrets Manager<a name="secretsmanager-compliance"></a>

Third\-party auditors assess the security and compliance of AWS Secrets Manager as part of multiple AWS compliance programs\. These include SOC, PCI, HIPAA, and others\.

For a list of AWS services in scope of specific compliance programs, see [AWS Services in Scope by Compliance Program](http://aws.amazon.com/compliance/services-in-scope/)\. For general information, see [AWS Compliance Programs](http://aws.amazon.com/compliance/programs/)\.

You can download third\-party audit reports using AWS Artifact\. For more information, see [Downloading Reports in AWS Artifact](https://docs.aws.amazon.com/artifact/latest/ug/downloading-documents.html)\.

Your compliance responsibility when using Secrets Manager is determined by the sensitivity of your data, your company's compliance objectives, and applicable laws and regulations\. AWS provides the following resources to help with compliance:
+ [Security and Compliance Quick Start Guides](http://aws.amazon.com/quickstart/?awsf.quickstart-homepage-filter=categories%23security-identity-compliance) – These deployment guides discuss architectural considerations and provide steps for deploying security\- and compliance\-focused baseline environments on AWS\.
+ [Architecting for HIPAA Security and Compliance Whitepaper ](https://d0.awsstatic.com/whitepapers/compliance/AWS_HIPAA_Compliance_Whitepaper.pdf) – This whitepaper describes how companies can use AWS to create HIPAA\-compliant applications\.
+ [AWS Compliance Resources](http://aws.amazon.com/compliance/resources/) – This collection of workbooks and guides might apply to your industry and location\.
+ *AWS Config* assesses how well your resource configurations comply with internal practices, industry guidelines, and regulations\. For more information, see [Audit AWS Secrets Manager secrets for compliance by using AWS Config](configuring-awsconfig-rules.md)\.
+ *AWS Security Hub* provides a comprehensive view of your security state within AWS that helps you check your compliance with security industry standards and best practices\. 

  The AWS Foundational Security Best Practices standard is a set of controls that detects when your deployed accounts and resources deviate from security best practices\. Security Hub provides a set of controls for Secrets Manager that allows you to continuously evaluate and identify areas of deviation from best practices\. For more information, see [AWS Foundational Security Best Practices controls](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-fsbp-controls.html#fsbp-secretsmanager-1)\.
+ *IAM Access Analyzer* analyzes policies, including condition statements in a policy, that allow an external entity to access a secret\. For more information, see [Previewing access with Access Analyzer](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-preview-access-apis.html)\.
+ *AWS Systems Manager* provides predefined runbooks for Secrets Manager\. For more information, see [Systems Manager Automation runbook reference for Secrets Manager](https://docs.aws.amazon.com/systems-manager-automation-runbooks/latest/userguide/automation-ref-asm.html)\.

