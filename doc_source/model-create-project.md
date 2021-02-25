# Creating your project<a name="model-create-project"></a>

An Amazon Lookout for Vision project is a grouping of the resources needed to create and manage a Lookout for Vision model\. A project manages the following:
+ **Dataset** – The images and image labels used to train a model\. For more information, see [Creating your dataset](model-create-dataset.md)\.
+ **Model** – The software that you train to detect anomalies\. You can have multiple versions of a model\. For more information, see [Training your model](model-train.md)\. 

We recommend that you use a project for a single use case, such as detecting anomalies in a single type of machine part\. 

**Note**  
You can use AWS CloudFormation to provision and configure Amazon Lookout for Vision projects\. For more information, see [Creating Amazon Lookout for Vision projects with AWS CloudFormation](creating-projects-with-cloudformation.md)\.

To view your projects, see [Viewing your projects](view-projects.md) or open the [Using the Amazon Lookout for Vision dashboard](dashboard.md)\. To delete a model, see [Deleting a model](delete-model.md)\. 

## Creating a project \(console\)<a name="create-project-console"></a>

The following procedure shows you how to create a project using the console\.

**To create a project \(console\)**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. In the left navigation pane, choose **Projects**\.

1. Choose **Create project**\. 

1. In **Project name**, enter a name for your project\.

1. Choose **Create project**\. The details page for your project is displayed\.

## Creating a project \(SDK\)<a name="create-project-sdk"></a>

You use the [CreateProject](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_CreateProject) operation to create an Amazon Lookout for Vision project\. The response from `CreateProject` includes the project name and the Amazon Resource Name \(ARN\) of the project\. Afterwards, call [CreateDataset](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_CreateDataset) to add a training and a test dataset to your project\. For more information, see [Creating a dataset with a manifest file \(SDK\)](create-dataset-sdk.md)\. 

To view the projects that you have created in a project, call `ListProjects`\. For more information, see [Viewing your projects](view-projects.md)\. 

**To create a project \(SDK\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with `AmazonLookoutVisionFullAccess` and permissions\. For more information, see [Step 2: Create an IAM administrator user and group](su-account-user.md)\.

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to create a model\.

------
#### [ CLI ]

   Change the value of `project-name` to the name that you want to use for the project\.

   ```
   aws lookoutvision create-project --project-name project name
   ```

------
#### [ Python ]

   In the function `main`, change the `project_name` to the name that you want to use for the project\.

   ```
   # Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
   # SPDX-License-Identifier: Apache-2.0
   
   import boto3
   
   from botocore.exceptions import ClientError
   
   def create_project(project_name):
       """
       Creates a new Amazon Lookout for Vision project.
       param: project_name: The name for the new project.
       """
   
       try:
   
           client = boto3.client("lookoutvision")
   
           # Create a project
           print("Creating project:" + project_name)
           response = client.create_project(ProjectName=project_name)
           print("project ARN: " + response["ProjectMetadata"]["ProjectArn"])
           print("Done...")
   
       except ClientError as err:
           print("Service error: " + format(err))
           raise
   
   
   def main():
   
       project_name = "my-project"  # Change to the desired name.
       create_project(project_name)
   
   
   if __name__ == "__main__":
       main()
   ```

------