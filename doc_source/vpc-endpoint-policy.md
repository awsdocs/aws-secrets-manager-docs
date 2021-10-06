# Create an endpoint policy for your Secrets Manager VPC endpoint<a name="vpc-endpoint-policy"></a>

Once you create a Secrets Manager VPC endpoint, you can attach an endpoint policy to control secrets\-related activity on the endpoint\. For example, you can attach an endpoint policy to define the performed Secrets Manager actions, actions performed on the secrets, the IAM users or roles performing these actions, and the accounts accessed through the VPC endpoint\. For additional information about endpoint policies, including a list of the AWS services supporting endpoint policies, see [Using VPC Endpoint policies](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-access.html#vpc-endpoints-policies)\. 

To add a policy to your secret, on the **Secret details** page, choose **Edit permissions**\.

**Note**  
AWS does not share VPC endpoints across AWS services\. If you use VPC endpoints for multiple AWS services, such as Secrets Manager and Amazon S3, you must attach a distinct policy to each endpoint\.

 **Example: Enable access to the Secrets Manager endpoint for a specific account** 

The following example grants access to all users and roles in account `123456789012`\.

```
{
         "Statement": [
            {
              "Sid": "AccessSpecificAccount",
              "Principal": {"AWS": "123456789012"},
              "Action": "secretsmanager:*",
              "Effect": "Allow",
              "Resource": "*"
            }
          ]
      }
```

 **Example: Enable access to a single secret on the Secrets Manager endpoint** 

The following example restricts access to only the specified secret\. 

```
      {
         "Statement": [
            {
              "Principal": "*",
              "Action": "secretsmanager:*",
              "Effect": "Allow",
              "Resource": [
                      "arn:aws:secretsmanager:us-east-2:111122223333:secret:SecretName-a1b2c3"
                      ]
            }
          ]
      }
```