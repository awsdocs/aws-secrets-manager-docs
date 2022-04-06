# How Amazon EventBridge uses AWS Secrets Manager<a name="integrating_how-services-use-secrets_EVlong"></a>

Amazon EventBridge is a serverless event bus service that you can use to connect your applications with data from a variety of sources\. 

Amazon EventBridge API destinations are HTTP endpoints that you can invoke as the target of an EventBridge rule\. When you create an API destination, you specify a connection to use for it, which EventBridge stores in a secret in Secrets Manager\. The cost of storing the Secrets Manager secret is included with the charge for using an API destination\. For more information, see [API destinations](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-api-destinations.html#eb-api-destination-connection)\.