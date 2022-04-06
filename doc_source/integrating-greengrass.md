# Use AWS Secrets Manager secrets in AWS IoT Greengrass<a name="integrating-greengrass"></a>

AWS IoT Greengrass is software that extends cloud capabilities to local devices\. This enables devices to collect and analyze data closer to the source of information, react autonomously to local events, and communicate securely with each other on local networks\. 

AWS IoT Greengrass lets you authenticate with services and applications from Greengrass devices without hard\-coding passwords, tokens, or other secrets\. You can use AWS Secrets Manager to securely store and manage your secrets in the cloud\. AWS IoT Greengrass extends Secrets Manager to Greengrass core devices, so your connectors and Lambda functions can use local secrets to interact with services and applications\. 

To integrate a secret into a Greengrass group, you create a group resource that references the Secrets Manager secret\. This secret resource references the cloud secret by using the associated ARN\. To learn how to create, manage, and use secret resources, see [Working with Secret Resources](https://docs.aws.amazon.com/greengrass/latest/developerguide/secrets-using.html) in the AWS IoT Developer Guide\. 

To deploy secrets to the AWS IoT Greengrass Core, see [Deploy secrets to the AWS IoT Greengrass core\.](https://docs.aws.amazon.com/greengrass/latest/developerguide/secrets.html)