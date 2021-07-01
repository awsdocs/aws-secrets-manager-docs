# Integrating Secrets Manager secrets with Kubernetes Secrets Store CSI Driver<a name="integrating_csi_driver"></a>

**Note**

CSI Driver does not work with EKS Fargate.  

Kubernetes provides a key/value store, etcd, that allows you to create key/value pairs and make them available to applications running in containers or pods though a file system interface. Kubernetes community offers a Secret Store Container Storage Interface (CSI) Driver

that you can use to provide secrets using the same standard file system interface. The Secret Store CSI Driver uses a plugable interface allowing secrets providers, such as AWS Secrets Manager or AWS Parameter Store, to write adapter plugins to get secrets from the given provider rather than from the standard K8s etcd store.

With the launch of AWS Secrets Manager and Configuration Provider (ASCP), you now have a plugin for the industry-standard Kubernetes Secrets Store Container Storage Interface (CSI) Driver

used for providing secrets to applications operating on Amazon Elastic Kubernetes Service. You can use ASCP to provide compatibility for legacy Kubernetes workloads that received secrets through the file system or Kubernetes etcd.

 Previously, you stored secrets as plaintext secret in configuration files or you used Kubernetes etcd to make your secrets visible through the file system. And you wrote custom code to rotate secrets, creating a maintenance challenge for your organization. To configure secure access to your pods, you created multiple clusters to control access, increasing operational load.

With ASCP, you can securely store and manage your secrets in Secrets Manager, as well as retrieve them through your application workloads running on Kubernetes. You no longer have to write custom code for your applications. You can also use IAM and resource policies for your secret, to limit and restrict access to specific Kubernetes pods inside a cluster. The policies controls accessibility to secrets by an individual pod. ASCP can use the optional rotation reconciler component to ensure retrieval of the latest secret from the secret provider. With the rotation reconciler enabled, ASCP ensures your applications always receive the latest version of the secret, enabling you to benefit from the lifecycle management capabilities of Secrets Manager. 

# Prerequisites

Before you can configure ASCP and Kubernetes, you must have the following items:
+ [An AWS account]
+ [An AWS Identity and Access Management account with permissions to modify an Secrets Manager.]
+ [IAM roles for service accounts (IRSA) configured.]
+ [You use the IAM roles for IRSA to limit secret access to your pods. When you set this up, the provider retrieves the pod identity and exchange this identity for an IAM role. ASCP assumes the IAM role of the pod and only retrieves secrets from Secrets Manager authorized for the pod. This prevents the container from accessing secrets intended for another container.]
+ [Specifically you need the following IAM policy permissions:]
   + [secretsmanager:GetSecretValue]
   + [secretsmanager:SecretsManager:DescribeSecret]
   + [ssm:GetParameters]
+ [Amazon EKS Version 1.17 or later.]
+ [Permissions to modify your Kubernetes cluster.]
+ [AWS CLI and kubectl installed.]
+ [helm]
+ [eksctl]

# Installing the CSI driver

Use these steps to deploy ASCP and Kubernetes:

1. Install the Kubernetes secrets store container storage interface (CSI) driver using the following helm commands.
   From the CLI where you have installed kubectl, run the following commands to install the CSI driver:
   ```
   helm repo add secrets-store-csi-driver https://raw.githubusercontent.com/kubernetes-sigs/secrets-store-csi-driver/master/charts
   helm -n kube-system install csi-secrets-store secrets-store-csi-driver/secrets-store-csi-driver --set grpcSupportedProviders="aws"
   ```

   Your output should be similar to this:
   ```
   csi-secrets-store-qp9r8         3/3     Running   0          4m
   csi-secrets-store-zrjt2         3/3     Running   0          4m
   ```
2. If you do not need to periodically pull updated secrets, initialize the driver:
   ```
   helm -n kube-system install csi-secrets-store secrets-store-csi-driver/secrets-store-csi-driver --set grpcSupportedProviders=”aws” 
   ```
3. If you do want to enable automated rotation for your driver using the rotation reconciler feature, use the following command:
   ```
   helm -n kube-system install csi-secrets-store secrets-store-csi-driver/secrets-store-csi-driver --set enableSecretRotation=true --set rotationPollInterval=3600s
   ```
   You can adjust the rotation to achieve a balance of API calls and rotation frequency.

**Note**

This is an alpha feature.

4. Install the AWS Secrets and Configuration Provider using a deployment YAML.
   ```
   curl -s https://raw.githubusercontent.com/aws/secrets-store-csi-driver-provider-aws/main/deployment/aws-provider-installer.yaml | kubectl apply -f -
   ```
5. Creating and deploying the SecretProviderClass custom resource.

   To use the Secrets Store CSI Driver, you must create a SecretProviderClass custom resource. The custom resource provides driver configurations and provider-specific parameters to the CSI driver. The SecretProviderClass resource should contain the following components, at a minimum:

   ```
    apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
    kind: SecretProviderClass
    metadata:
      name: aws-secrets
    spec:
      provider: aws
      parameters:
        objects: | 
            - objectName: "MySecret"
              objectType: "secretsmanager"s
    ```
    To implement ASCP, you create the SecretProviderClass with more details about retrieving configurations from Parameter Store and secrets from Secrets Manager. The SecretProviderClass must be in the same namespace as the referenced pod. Here's an example configuration:

    ```
    apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
    kind: SecretProviderClass
    metadata:
    name: aws-secrets
    spec:
    provider: aws
    parameters:# provider-specific parameters
    objects: |
    array:
    - |
    objectName: "arn:aws:secretsmanager:us-east-1
    :[ACCOUNT]:secret:MySecret-00AABB"
    objectVersion: "00112233AABB00112233445566778899"
    - |
    objectName: "MyConfigValue2"
    objectType: "ssm"# object types: secretsmanager for secrets and ssm for
    configuration values
    objectVersion: "1"
    - |
    objectName: "MySecret2"
    objectType: "secretsmanager"
    objectVersion: "00112233AABB00112233445566778899"
    - |
    objectName: "MySecret3"
    objectType: "secretsmanager"
    objectVersionStage: "AWSCURRENT"# [OPTIONAL] object version stage, default to
    latest if empty
    ```
6. After you create the SecretProviderClass in the previous step, you can use the following sample code to deploy your application or pod.

   ```
    kind: Pod
    apiVersion: v1
    metadata:
      name: nginx-deployment
    spec:
      serviceAccountName: nginx-deployment-sa
      containers:
      - image: nginx
        name: nginx
        volumeMounts:
        - name: secrets-store-inline
          mountPath: "/mnt/secrets-store"
          readOnly: true
      volumes:
      - name: secrets-store-inline
        csi:
          driver: secrets-store.csi.k8s.io
          readOnly: true
          volumeAttributes:
            secretProviderClass: "aws-secrets"
   ```
# SecretProviderClass options

The SecretProviderClass has the following format:
        ```
        apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
        kind: SecretProviderClass
        metadata:
           name: <NAME>
        spec:
          provider: aws
          parameters:
        ```
The parameters section contains the details of the mount request and has one of three fields:

+ objects - A string containing a YAML declaration of the secrets to mount.
    ```
    parameters:
      objects: |
          - objectName: "MySecret"
            objectType: "secretsmanager"
            
    ```
+ region (Optional) - Specifies the AWS region to use when retrieving secrets from Secrets Manager or Parameter Store. If you don't use this field, the provider lookups the region from the annotation on the node. This lookup adds overhead to mount requests so clusters using large numbers of pods benefit from providing the region here.

+ pathTranslation (Optional) - Specifies a substitution character to use if you add the path separator character, such as slash on Linux, in the file name. If a Secret or parameter name contains the path separator, failures occur when the provider tries to create a mounted file using the name. When not specified, the underscore character is used, and My/Path/Secret mounts as My_Path_Secret. This pathTranslation value can either be the string False or a single character string. When set to False, no character substitution is performed.

The objects field of SecretProviderClass can contain the following sub-fields:

+ objectName(Required) - Specifies the name of the secret or parameter. For Secrets Manager, the SecretId parameter can be either the friendly name or full ARN of the secret. For SSM Parameter Store, this must be the Name of the parameter and cannot contain a full ARN.

+ objectType - Optional if you use a Secrets Manager ARN for the objectName. If you don't use a Secrets Manager ARN, SecretProviderClass requires the field and can be either secretsmanager or ssmparameter.

+ objectAlias(Optional) - Specifies the file name to mount the secret. If not specified, it defaults to objectName.

+ objectVersion(Optional) - Recommended to not use this field as it requires updating if you update the secret.

+ objectVersionLabel(Optional) - Specifies the alias used for the version. SecretProviderClass uses the most recent version by default.

