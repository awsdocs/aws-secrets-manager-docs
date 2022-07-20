# How AWS OpsWorks for Chef Automate uses AWS Secrets Manager<a name="integrating_how-services-use-secrets_opsworks-cm"></a>

AWS OpsWorks is a configuration management service that helps you configure and operate applications in a cloud enterprise by using Puppet or Chef\. 

When you create a new server in AWS OpsWorks CM, OpsWorks CM stores information for the server in a Secrets Manager secret with the prefix `opsworks-cm`\. The cost of the secret is included in the charge for AWS OpsWorks\. For more information, see [Integration with AWS Secrets Manager](https://docs.aws.amazon.com/opsworks/latest/userguide/data-protection.html#data-protection-secrets-manager) in the *AWS OpsWorks User Guide*\.