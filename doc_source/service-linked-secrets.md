# AWS Secrets Manager secrets managed by other AWS services<a name="service-linked-secrets"></a>

Some AWS services that store AWS Secrets Manager secrets on your behalf can [manage rotation](rotate-secrets_managed.md) for you\. The managing service might also restrict you from updating secrets or deleting them without a recovery period\. These secrets are typically named with a service ID prefix that indicates which service created them\. 

**To find secrets that are managed by other AWS services**
+ Do one of the following:
  + In the Secrets Manager console, in the search box, choose **Tag key** and then enter `aws:secretsmanager:owningService`\.
  + To show the managing service in the list of secrets, choose **Preferences** \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/preferences-gear.png)\), and then in the **Preferences** dialog box, turn on **Managed by**\.
  + In the AWS CLI, enter the following command using the service ID prefix:

    ```
    aws secretsmanager list-secrets --filter Key="owning-service",Values="<service ID prefix>"
    ```

## Services that manage secrets<a name="service-linked-secrets_services"></a>


****  

| Service ID | More information | 
| --- | --- | 
| appflow | [How Amazon AppFlow uses AWS Secrets Manager](integrating_how-services-use-secrets_appflow.md) | 
| databrew | [How AWS Glue DataBrew uses AWS Secrets Manager](integrating_how-services-use-secrets_databrew.md) | 
| directconnect | [How AWS Direct Connect uses AWS Secrets Manager](integrating_how-services-use-secrets_directconnect.md) | 
| events | [How Amazon EventBridge uses AWS Secrets Manager](integrating_how-services-use-secrets_events.md) | 
| opsworks\-cm | [How AWS OpsWorks for Chef Automate uses AWS Secrets Manager](integrating_how-services-use-secrets_opsworks-cm.md) | 
| rds | [How Amazon RDS uses AWS Secrets Manager](integrating_how-services-use-secrets_RDS.md)  | 
| sqlworkbench | [How Amazon Redshift query editor v2 uses AWS Secrets Manager](integrating_how-services-use-secrets_sqlworkbench.md) | 

For other services that integrate with Secrets Manager, see [AWS services that use AWS Secrets Manager secrets](integrating.md)\.