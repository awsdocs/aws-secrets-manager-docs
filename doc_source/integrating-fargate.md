# Integrating Secrets Manager with AWS Fargate<a name="integrating-fargate"></a>

AWS Fargate is a technology that you can use with Amazon ECS to run containers without managing servers or clusters of Amazon ECS instances\. With AWS Fargate, you no longer have to provision, configure, or scale clusters of virtual machines to run containers\. This removes the need to choose server types, decide when to scale your clusters, or optimize cluster packing\.

When you run your Amazon Amazon ECS tasks and services with the Fargate launch type or a Fargate capacity provider, you package your application in containers, specify the CPU and memory requirements, define networking and IAM policies, and launch the application\. Each Fargate task has its own isolation boundary and does not share the underlying kernel, CPU resources, memory resources, or elastic network interface with another task

You can configure Fargate interfaces to allow retrieval of secrets from Secrets Manager\. For more information, see [Fargate Task Networking](https://docs.aws.amazon.com/AmazonECS/latest/userguide/fargate-task-networking.html)