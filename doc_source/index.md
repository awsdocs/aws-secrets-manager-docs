# AWS Secrets Manager User Guide

-----
*****Copyright &copy; 2018 Amazon Web Services, Inc. and/or its affiliates. All rights reserved.*****

-----
Amazon's trademarks and trade dress may not be used in 
     connection with any product or service that is not Amazon's, 
     in any manner that is likely to cause confusion among customers, 
     or in any manner that disparages or discredits Amazon. All other 
     trademarks not owned by Amazon are the property of their respective
     owners, who may or may not be affiliated with, connected to, or 
     sponsored by Amazon.

-----
## Contents
+ [What Is AWS Secrets Manager?](intro.md)
   + [Support and Feedback for AWS Secrets Manager](support-and-feedback.md)
+ [Getting Started with AWS Secrets Manager](getting-started.md)
   + [Key Terms and Concepts for AWS Secrets Manager](terms-concepts.md)
   + [AWS Secrets Manager Tutorials](tutorials.md)
      + [Tutorial: Storing and Retrieving a Secret](tutorials_basic.md)
      + [Tutorial: Rotating a Secret for an AWS Database](tutorials_db-rotate.md)
      + [Tutorial: Rotating a User Secret with a Master Secret](tutorials_db-rotate-master.md)
+ [AWS Secrets Manager Best Practices](best-practices.md)
+ [Creating and Managing Secrets with AWS Secrets Manager](managing-secrets.md)
   + [Creating a Basic Secret](manage_create-basic-secret.md)
   + [Modifying a Secret](manage_update-secret.md)
   + [Retrieving the Secret Value](manage_retrieve-secret.md)
   + [Deleting and Restoring a Secret](manage_delete-restore-secret.md)
   + [Managing a Resource-based Policy for a Secret](manage_secret-policy.md)
+ [Rotating Your AWS Secrets Manager Secrets](rotating-secrets.md)
   + [Permissions Required to Automatically Rotate Secrets](rotating-secrets-required-permissions.md)
   + [Configuring Your Network to Support Rotating Secrets](rotation-network-rqmts.md)
   + [Rotating Secrets for Supported Amazon RDS Databases](rotating-secrets-rds.md)
      + [Enabling Rotation for an Amazon RDS Database Secret](enable-rotation-rds.md)
      + [Customizing the Lambda Rotation Function Provided by Secrets Manager](rotating-secrets-customize-rds-lambda.md)
   + [Rotating AWS Secrets Manager Secrets for Other Databases or Services](rotating-secrets-other.md)
      + [Rotating AWS Secrets Manager Secrets for Other Databases or Services](rotating-secrets-create-generic-template.md)
      + [Enabling Rotation for a Secret for Another Database or Service](enable-rotation-other.md)
   + [Understanding and Customizing Your Lambda Rotation Function](rotating-secrets-lambda-function-customizing.md)
      + [Overview of the Lambda Rotation Function](rotating-secrets-lambda-function-overview.md)
      + [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)
      + [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)
      + [Rotating AWS Secrets Manager Secrets For One User that Supports Multiple Credentials](rotating-secrets-one-user-multiple-passwords.md)
   + [Deleting Lambda Rotation Functions That You No Longer Need](rotating-secrets-managing-functions.md)
+ [Authentication and Access Control for AWS Secrets Manager](auth-and-access.md)
   + [Overview of Managing Access Permissions to Your Secrets Manager Secrets](auth-and-access_overview.md)
   + [Using Identity-based Policies (IAM Policies) for Secrets Manager](auth-and-access_identity-based-policies.md)
   + [Using Resource-based Policies for Secrets Manager](auth-and-access_resource-based-policies.md)
   + [Determining Access to a Secret](auth-and-access_determining-access.md)
+ [Monitor the Use of Your AWS Secrets Manager Secrets](monitoring.md)
+ [AWS Secrets Manager Reference](reference.md)
   + [Limits of AWS Secrets Manager](reference_limits.md)
   + [AWS Templates You Can Use to Create Lambda Rotation Functions](reference_available-rotation-templates.md)
      + [Secrets Manager Lambda Rotation Template: Generic Template That You Must Customize and Complete](reference_template_Generic.md)
      + [Secrets Manager Lambda Rotation Template: RDS MySQL Single User](reference_template_MySql_SingleUser.md)
      + [Secrets Manager Lambda Rotation Template: RDS MySQL Multiple User](reference_template_MySql_MultiUser.md)
      + [Secrets Manager Lambda Rotation Template: RDS PostgreSQL Single User](reference_template_PostgreSql_SingleUser.md)
      + [Secrets Manager Lambda Rotation Template: RDS PostgreSQL Multiple User](reference_template_PostgreSql_MultiUser.md)
   + [AWS Managed Policies Available for Use with AWS Secrets Manager](reference_available-policies.md)
   + [Actions, Resources, and Context Keys You Can Use in an IAM Policy or Secret Policy for AWS Secrets Manager](reference_iam-permissions.md)
+ [Troubleshooting AWS Secrets Manager](troubleshoot.md)
   + [Troubleshooting General Issues](troubleshoot_general.md)
   + [Troubleshooting AWS Secrets Manager Rotation of Secrets](org_troubleshoot_rotation.md)
+ [Calling the API by Making HTTP Query Requests](query-requests.md)
+ [Document History for AWS Secrets Manager](document-history.md)
+ [AWS Glossary](glossary.md)