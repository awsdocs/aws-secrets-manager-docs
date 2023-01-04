# How Amazon SageMaker uses AWS Secrets Manager<a name="integrating-sagemaker"></a>

SageMaker is a fully managed machine learning service\. With SageMaker, data scientists and developers can quickly and easily build and train machine learning models, and then directly deploy them into a production\-ready hosted environment\. It provides an integrated Jupyter authoring notebook instance for easy access to your data sources for exploration and analysis, so you don't have to manage servers\. 

You can associate Git repositories with your Jupyter notebook instances to save your notebooks in a source control environment that persists even if you stop or delete your notebook instance\. You can manage your private repositories credentials using Secrets Manager\. For more information, see [Associate Git Repositories with Amazon SageMaker Notebook Instances](https://docs.aws.amazon.com/sagemaker/latest/dg/nbi-git-repo.html) in the *Amazon SageMaker Developer Guide*\.

To import data from Databricks, Data Wrangler stores your JDBC URL in Secrets Manager\. For more information, see [Import data from Databricks \(JDBC\)](https://docs.aws.amazon.com/sagemaker/latest/dg/data-wrangler-import.html#data-wrangler-databricks)\.

To import data from Snowflake, Data Wrangler stores your credentials in a Secrets Manager secret\. For more information, see [Import data from Snowflake](https://docs.aws.amazon.com/sagemaker/latest/dg/data-wrangler-import.html#data-wrangler-snowflake)\.