# Automating secret creation in AWS CloudFormation<a name="integrating_cloudformation"></a>

You can use AWS CloudFormation to create and reference secrets from within your AWS CloudFormation stack template\. You can create a secret and then reference it from another part of the template\. For example, you can retrieve the user name and password from the new secret and then use that to define the user name and password for a new database\. You can create and attach resource\-based policies to a secret\. You can also configure rotation by defining a Lambda function in your template and associating the function with your new secret as its rotation Lambda function\. 

You create an AWS CloudFormation template in either JSON or YAML\. AWS CloudFormation processes the template and builds the resources that are defined in the template\. You can use templates to create a new copy of your infrastructure whenever you need it\. For example, you can duplicate your test infrastructure to create the public version\. You can also share the infrastructure as a simple text file, so other people can replicate the resources\.

Secrets Manager provides the following resource types that you can use to create secrets in an AWS CloudFormation template:
+ **[ AWS::SecretsManager::Secret ](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secret.html)** – Creates a secret and stores it in Secrets Manager\. You can specify a password or Secrets Manager can generate one for you\. You can also create an empty secret and then update it later using the parameter `SecretString.` 
+ **[ AWS::SecretsManager::ResourcePolicy ](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-resourcepolicy.html)** – Creates a resource\-based policy and attaches it to the secret\. A resource\-based policy controls who can perform actions on the secret\.
+ **[ AWS::SecretsManager::RotationSchedule ](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-rotationschedule.html)** – Configures a secret to perform automatic periodic rotation using the specified Lambda rotation function\.
+ **[ AWS::SecretsManager::SecretTargetAttachment ](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secrettargetattachment.html)** – Configures the secret with the details about the service or database that Secrets Manager needs to rotate the secret\. For example, for an Amazon RDS DB instance, Secrets Manager adds the connection details and database engine type as entries in the `SecureString` property of the secret\.

## Examples<a name="integrating_cloudformation_examples"></a>

The following example templates create a secret and an Amazon RDS MySQL DB instance using the credentials in the secret as the user and password\. The secret has a resource\-based policy attached that defines who can access the secret\. The template also creates a Lambda rotation function and configures the secret to automatically rotate every 30 days\.

**Note**  
The [JSON specification](https://json.org/) doesn't support comments\. See the [YAML](http://yaml.org/spec/1.2/spec.html) version later on this page for comments\.

### JSON<a name="integrating_cloudformation_examples-1.json"></a>

```
    {
        "Transform": "AWS::SecretsManager-2020-07-23",
        "Description": "This is an example template to demonstrate CloudFormation resources for Secrets Manager",
        "Resources": {
            "TestVPC": {
                "Type": "AWS::EC2::VPC",
                "Properties": {
                    "CidrBlock": "10.0.0.0/16",
                    "EnableDnsHostnames": true,
                    "EnableDnsSupport": true
                }
            },
            "TestSubnet01": {
                "Type": "AWS::EC2::Subnet",
                "Properties": {
                    "CidrBlock": "10.0.96.0/19",
                    "AvailabilityZone": {
                        "Fn::Select": [
                            "0",
                            {
                                "Fn::GetAZs": {
                                    "Ref": "AWS::Region"
                                }
                            }
                        ]
                    },
                    "VpcId": {
                        "Ref": "TestVPC"
                    }
                }
            },
            "TestSubnet02": {
                "Type": "AWS::EC2::Subnet",
                "Properties": {
                    "CidrBlock": "10.0.128.0/19",
                    "AvailabilityZone": {
                        "Fn::Select": [
                            "1",
                            {
                                "Fn::GetAZs": {
                                    "Ref": "AWS::Region"
                                }
                            }
                        ]
                    },
                    "VpcId": {
                        "Ref": "TestVPC"
                    }
                }
            },
            "SecretsManagerVPCEndpoint": {
                "Type": "AWS::EC2::VPCEndpoint",
                "Properties": {
                    "SubnetIds": [
                        {
                            "Ref": "TestSubnet01"
                        },
                        {
                            "Ref": "TestSubnet02"
                        }
                    ],
                    "SecurityGroupIds": [
                        {
                            "Fn::GetAtt": [
                                "TestVPC",
                                "DefaultSecurityGroup"
                            ]
                        }
                    ],
                    "VpcEndpointType": "Interface",
                    "ServiceName": {
                        "Fn::Sub": "com.amazonaws.${AWS::Region}.secretsmanager"
                    },
                    "PrivateDnsEnabled": true,
                    "VpcId": {
                        "Ref": "TestVPC"
                    }
                }
            },
            "MyRDSInstanceRotationSecret": {
                "Type": "AWS::SecretsManager::Secret",
                "Properties": {
                    "Description": "This is my rds instance secret",
                    "GenerateSecretString": {
                        "SecretStringTemplate": "{\"username\": \"admin\"}",
                        "GenerateStringKey": "password",
                        "PasswordLength": 16,
                        "ExcludeCharacters": "\"@/\\"
                    },
                    "Tags": [
                        {
                            "Key": "AppName",
                            "Value": "MyApp"
                        }
                    ]
                }
            },
            "MyDBInstance": {
                "Type": "AWS::RDS::DBInstance",
                "Properties": {
                    "AllocatedStorage": 20,
                    "DBInstanceClass": "db.t2.micro",
                    "Engine": "mysql",
                    "DBSubnetGroupName": {
                        "Ref": "MyDBSubnetGroup"
                    },
                    "MasterUsername": {
                        "Fn::Sub": "{{resolve:secretsmanager:${MyRDSInstanceRotationSecret}::username}}"
                    },
                    "MasterUserPassword": {
                        "Fn::Sub": "{{resolve:secretsmanager:${MyRDSInstanceRotationSecret}::password}}"
                    },
                    "BackupRetentionPeriod": 0,
                    "VPCSecurityGroups": [
                        {
                            "Fn::GetAtt": [
                                "TestVPC",
                                "DefaultSecurityGroup"
                            ]
                        }
                    ]
                }
            },
            "MyDBSubnetGroup": {
                "Type": "AWS::RDS::DBSubnetGroup",
                "Properties": {
                    "DBSubnetGroupDescription": "Test Group",
                    "SubnetIds": [
                        {
                            "Ref": "TestSubnet01"
                        },
                        {
                            "Ref": "TestSubnet02"
                        }
                    ]
                }
            },
            "SecretRDSInstanceAttachment": {
                "Type": "AWS::SecretsManager::SecretTargetAttachment",
                "Properties": {
                    "SecretId": {
                        "Ref": "MyRDSInstanceRotationSecret"
                    },
                    "TargetId": {
                        "Ref": "MyDBInstance"
                    },
                    "TargetType": "AWS::RDS::DBInstance"
                }
            },
            "MySecretRotationSchedule": {
                "Type": "AWS::SecretsManager::RotationSchedule",
                "DependsOn": "SecretRDSInstanceAttachment",
                "Properties": {
                    "SecretId": {
                        "Ref": "MyRDSInstanceRotationSecret"
                    },
                    "HostedRotationLambda": {
                        "RotationType": "MySQLSingleUser",
                        "RotationLambdaName": "SecretsManagerRotation",
                        "VpcSecurityGroupIds": {
                            "Fn::GetAtt": [
                                "TestVPC",
                                "DefaultSecurityGroup"
                            ]
                        },
                        "VpcSubnetIds": {
                            "Fn::Join": [
                                ",",
                                [
                                    {
                                        "Ref": "TestSubnet01"
                                    },
                                    {
                                        "Ref": "TestSubnet02"
                                    }
                                ]
                            ]
                        }
                    },
                    "RotationRules": {
                        "AutomaticallyAfterDays": 30
                    }
                }
            }
        }
    }
```

### YAML<a name="integrating_cloudformation_examples-1.yaml"></a>

```yaml
---
Transform: AWS::SecretsManager-2020-07-23
Description: This is an example template to demonstrate CloudFormation resources for
  Secrets Manager
Resources:

  #This is the VPC that the rotation Lambda function and the RDS instance will be placed in
  TestVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
      
  # Subnet that the rotation Lambda function and the RDS instance will be placed in 
  TestSubnet01:
    Type: AWS::EC2::Subnet
    Properties:
      CidrBlock: 10.0.96.0/19
      AvailabilityZone:
        Fn::Select:
        - '0'
        - Fn::GetAZs:
            Ref: AWS::Region
      VpcId:
        Ref: TestVPC
  TestSubnet02:
    Type: AWS::EC2::Subnet
    Properties:
      CidrBlock: 10.0.128.0/19
      AvailabilityZone:
        Fn::Select:
        - '1'
        - Fn::GetAZs:
            Ref: AWS::Region
      VpcId:
        Ref: TestVPC
        
  #VPC endpoint that will enable the rotation Lambda function to make api calls to Secrets Manager 
  SecretsManagerVPCEndpoint:
    Type: AWS::EC2::VPCEndpoint
    Properties:
      SubnetIds:
      - Ref: TestSubnet01
      - Ref: TestSubnet02
      SecurityGroupIds:
      - Fn::GetAtt:
        - TestVPC
        - DefaultSecurityGroup
      VpcEndpointType: Interface
      ServiceName:
        Fn::Sub: com.amazonaws.${AWS::Region}.secretsmanager
      PrivateDnsEnabled: true
      VpcId:
        Ref: TestVPC
  
  #This is a Secret resource with a randomly generated password in its SecretString JSON.
  MyRDSInstanceRotationSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: This is my rds instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "admin"}'
        GenerateStringKey: password
        PasswordLength: 16
        ExcludeCharacters: "\"@/\\"
      Tags:
      - Key: AppName
        Value: MyApp
        
  #This is an RDS instance resource. Its master username and password use dynamic references to resolve values from 
  #SecretsManager. The dynamic reference guarantees that CloudFormation will not log or persist the resolved value 
  #We sub the Secret resource's logical id in order to construct the dynamic reference, since the Secret's name is being #generated by CloudFormation
  MyDBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      AllocatedStorage: 20
      DBInstanceClass: db.t2.micro
      Engine: mysql
      DBSubnetGroupName:
        Ref: MyDBSubnetGroup
      MasterUsername:
        Fn::Sub: "{{resolve:secretsmanager:${MyRDSInstanceRotationSecret}::username}}"
      MasterUserPassword:
        Fn::Sub: "{{resolve:secretsmanager:${MyRDSInstanceRotationSecret}::password}}"
      BackupRetentionPeriod: 0
      VPCSecurityGroups:
      - Fn::GetAtt:
        - TestVPC
        - DefaultSecurityGroup
        
  #Database subnet group for the RDS instance 
  MyDBSubnetGroup:
    Type: AWS::RDS::DBSubnetGroup
    Properties:
      DBSubnetGroupDescription: Test Group
      SubnetIds:
      - Ref: TestSubnet01
      - Ref: TestSubnet02
      
  #This is a SecretTargetAttachment resource which updates the referenced Secret resource with properties about
  #the referenced RDS instance
  SecretRDSInstanceAttachment:
    Type: AWS::SecretsManager::SecretTargetAttachment
    Properties:
      SecretId:
        Ref: MyRDSInstanceRotationSecret
      TargetId:
        Ref: MyDBInstance
      TargetType: AWS::RDS::DBInstance
  
  #This is a RotationSchedule resource. It configures rotation of password for the referenced secret using a rotation lambda function
  #The first rotation happens at resource creation time, with subsequent rotations scheduled according to the rotation rules
  #We explicitly depend on the SecretTargetAttachment resource being created to ensure that the secret contains all the
  #information necessary for rotation to succeed
  MySecretRotationSchedule:
    Type: AWS::SecretsManager::RotationSchedule
    DependsOn: SecretRDSInstanceAttachment
    Properties:
      SecretId:
        Ref: MyRDSInstanceRotationSecret
      HostedRotationLambda:
        RotationType: MySQLSingleUser
        RotationLambdaName: SecretsManagerRotation
        VpcSecurityGroupIds:
          Fn::GetAtt:
          - TestVPC
          - DefaultSecurityGroup
        VpcSubnetIds:
          Fn::Join:
          - ","
          - - Ref: TestSubnet01
            - Ref: TestSubnet02
      RotationRules:
        AutomaticallyAfterDays: 30
```
