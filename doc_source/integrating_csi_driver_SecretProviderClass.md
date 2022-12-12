# `SecretProviderClass`<a name="integrating_csi_driver_SecretProviderClass"></a>

You use YAML to describe which secrets to mount in Amazon EKS using the ASCP\. For examples, see [Identify which secrets to mount](integrating_csi_driver.md#integrating_csi_driver_mount)\.

```
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
   name: <NAME>
spec:
  provider: aws
  parameters:
    region:
    failoverRegion:
    pathTranslation:
    objects:
```

The field `parameters` contains the details of the mount request:

**region**  
\(Optional\) The AWS Region of the secret\. If you don't use this field, the ASCP looks up the Region from the annotation on the node\. This lookup adds overhead to mount requests, so we recommend that you provide the Region for clusters that use large numbers of pods\.  
If you also specify `failoverRegion`, the ASCP tries to retrieve the secret from both Regions\. If either Region returns a 4xx error, for example for an authentication issue, the ASCP does not mount either secret\. If the secret is retrieved successfully from `region`, then the ASCP mounts that secret value\. If the secret is not retrieved successfully from `region`, but it is retrieved successfully from `failoverRegion`, then the ASCP mounts that secret value\.

**failoverRegion**  
\(Optional\) If you include this field, the ASCP tries to retrieve the secret from the Regions defined in `region` and this field\. If either Region returns a 4xx error, for example for an authentication issue, the ASCP does not mount either secret\. If the secret is retrieved successfully from `region`, then the ASCP mounts that secret value\. If the secret is not retrieved successfully from `region`, but it is retrieved successfully from `failoverRegion`, then the ASCP mounts that secret value\. For an example of how to use this field, see [Define a failover Region for a multi\-Region secret](integrating_csi_driver.md#integrating_csi_driver_example_3)\.

**pathTranslation**  
\(Optional\) A single substitution character to use if the file name in Amazon EKS will contain the path separator character, such as slash \(/\) on Linux\. The ASCP can't create a mounted file that contains a path separator character\. Instead, the ASCP replaces the path separator character with a different character\. If you don't use this field, the replacement character is underscore \(\_\), so for example, `My/Path/Secret` mounts as `My_Path_Secret`\.   
To prevent character substitution, enter the string `False`\.

**objects**  
A string containing a YAML declaration of the secrets to be mounted\. We recommend using a YAML multi\-line string or pipe \(\|\) character\.    
**objectName**  
The name or full ARN of the secret\. If you use the ARN, you can omit `objectType`\. This field becomes the file name of the secret in the Amazon EKS pod unless you specify `objectAlias`\. If you use an ARN, the Region in the ARN must match the field `region`\. If you include a `failoverRegion`, this field represents the primary `objectName`\.  
**objectType**  
Required if you don't use a Secrets Manager ARN for `objectName`\. Can be either `secretsmanager` or `ssmparameter`\.   
**objectAlias**  
\(Optional\) The file name of the secret in the Amazon EKS pod\. If you don't specify this field, the `objectName` appears as the file name\.  
**objectVersion**  
\(Optional\) The version ID of the secret\. Not recommended because you must update the version ID every time you update the secret\. By default the most recent version is used\. If you include a `failoverRegion`, this field represents the primary `objectVersion`\.  
**objectVersionLabel**  
\(Optional\) The alias for the version\. The default is the most recent version AWSCURRENT\. For more information, see [Version](getting-started.md#term_version)\. If you include a `failoverRegion`, this field represents the primary `objectVersionLabel`\.  
**jmesPath **  
\(Optional\) A map of the keys in the secret to the files to be mounted in Amazon EKS\. To use this field, your secret value must be in JSON format\. If you use this field, you must include the subfields `path` and `objectAlias`\.    
**path**  
A key from a key/value pair in the JSON of the secret value\.  
**objectAlias**  
The file name to be mounted in the Amazon EKS pod\.  
**failoverObject**  
\(Optional\) If you specify this field, the ASCP tries to retrieve both the secret specified in the primary `objectName` and the secret specified in the `failoverObject` `objectName` sub\-field\. If either returns a 4xx error, for example for an authentication issue, the ASCP does not mount either secret\. If the secret is retrieved successfully from the primary `objectName`, then the ASCP mounts that secret value\. If the secret is not retrieved successfully from the primary `objectName`, but it is retrieved successfully from the failover `objectName`, then the ASCP mounts that secret value\. If you include this field, you must include the field `objectAlias`\. For an example of how to use this field, see [Choose a failover secret to mount](integrating_csi_driver.md#integrating_csi_driver_example_4)\.  
You typically use this field when the failover secret isn't a replica\. For an example of how to specify a replica, see [Define a failover Region for a multi\-Region secret](integrating_csi_driver.md#integrating_csi_driver_example_3)\.    
**objectName**  
The name or full ARN of the failover secret\. If you use an ARN, the Region in the ARN must match the field `failoverRegion`\.  
**objectVersion**  
\(Optional\) The version ID of the secret\. Must match the primary `objectVersion`\. Not recommended because you must update the version ID every time you update the secret\. By default the most recent version is used\.   
**objectVersionLabel**  
\(Optional\) The alias for the version\. The default is the most recent version AWSCURRENT\. For more information, see [Version](getting-started.md#term_version)\. 