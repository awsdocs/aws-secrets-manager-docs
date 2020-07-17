# AWS Secrets Manager Tutorials<a name="tutorials"></a>

Use the tutorials in this section to learn how to perform tasks using AWS Secrets Manager\.

[Tutorial: Creating and Retrieving a Secret](tutorials_basic.md)  
Get up and running with step\-by\-step instructions to create a secret and then retrieve it\. This tutorial does not require a pre\-configured AWS database\. 

[Tutorial: Rotating a Secret for an AWS Database](tutorials_db-rotate.md)  
Create a secret for an Amazon RDS MySQL database\. Then rotate the secret used to access the database, and configure the secret to rotate on a schedule\.

[Tutorial: Rotating a User Secret with a Master Secret](tutorials_db-rotate-master.md)  
Use the previous tutorial's secret as a *master* secret that can rotate a separate *user* secret for an Amazon RDS MySQL database\. Then rotate the user secret by signing in as the master secret and alternating users\.