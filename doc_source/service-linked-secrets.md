# Secrets managed by other AWS services<a name="service-linked-secrets"></a>

Some AWS services that store secrets on your behalf restrict you from updating them or deleting them without a recovery period\. These secrets are typically named with a service ID prefix that indicates which service created them\. For more information, see:


****  

| Service ID | More information | 
| --- | --- | 
| appflow | [How Amazon AppFlow uses AWS Secrets Manager](integrating_how-services-use-secrets_appflow.md) | 
| databrew | [How AWS Glue DataBrew uses AWS Secrets Manager](integrating_how-services-use-secrets_databrew.md) | 
| directconnect | [How AWS Direct Connect uses AWS Secrets Manager](integrating_how-services-use-secrets_directconnect.md) | 
| events | [How Amazon EventBridge uses AWS Secrets Manager](integrating_how-services-use-secrets_events.md) | 
| opsworks\-cm | [How AWS OpsWorks for Chef Automate uses AWS Secrets Manager](integrating_how-services-use-secrets_opsworks-cm.md) | 
| sqlworkbench | [How Amazon Redshift query editor v2 uses AWS Secrets Manager](integrating_how-services-use-secrets_sqlworkbench.md) | 

**To find secrets that are managed by other AWS services**
+ Do one of the following:
  + In the Secrets Manager console, in the search box, choose **Tag key** and then enter `aws:secretsmanager:owningService`\.
  + In the AWS CLI, enter the following command:

    ```
    aws secretsmanager list-secrets --filter Key="tag-key",Values="aws:secretsmanager:owningService"
    ```

For other services that integrate with Secrets Manager, see [AWS services that use Secrets Manager secrets](integrating.md)\.