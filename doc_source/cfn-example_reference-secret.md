# Retrieve a secret in an AWS CloudFormation resource<a name="cfn-example_reference-secret"></a>

This example retrieves the `username` and `password` values stored in the `MyRDSSecret` secret and uses them as the username and password for the Amazon RDS DB instance\. To retrieve the secret, the template uses a *dynamic reference*\. For more information, see [Using dynamic references to specify Secrets Manager secrets](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/dynamic-references.html#dynamic-references-secretsmanager)\.

The `MyRDSSecret` secret value looks like this:

```
{
    "engine": "mysql",
    "username": "admin",
    "password": "EXAMPLE-PASSWORD",
    "host": "my-database-endpoint.us-east-2.rds.amazonaws.com",
    "dbname": "myDatabase",
    "port": "3306"
}
```

For information about creating resources with AWS CloudFormation, see [Learn template basics](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/gettingstarted.templatebasics.html) in the AWS CloudFormation User Guide\.

## JSON<a name="cfn-example_reference-secret.json"></a>

```
{
    "MyRDSInstance": {
        "Type": "AWS::RDS::DBInstance",
        "Properties": {
            "DBName": "MyRDSInstance",
            "AllocatedStorage": "20",
            "DBInstanceClass": "db.t2.micro",
            "Engine": "mysql",
            "MasterUsername": "{{resolve:secretsmanager:MyRDSSecret:SecretString:username}}",
            "MasterUserPassword": "{{resolve:secretsmanager:MyRDSSecret:SecretString:password}}"
        }
    }
}
```

## YAML<a name="cfn-example_reference-secret.yaml"></a>

```
  MyRDSInstance:
    Type: 'AWS::RDS::DBInstance'
    Properties:
      DBName: MyRDSInstance
      AllocatedStorage: '20'
      DBInstanceClass: db.t2.micro
      Engine: mysql
      MasterUsername: '{{resolve:secretsmanager:MyRDSSecret:SecretString:username}}'
      MasterUserPassword: '{{resolve:secretsmanager:MyRDSSecret:SecretString:password}}'
```