# Viewing your models<a name="view-models"></a>

A project can have multiple versions of a model\. You can use the console to view the models in a project\. You can also use the `ListModels` operation\.

## Viewing your models \(console\)<a name="view-models-console"></a>

Perform the steps in the following procedure to view the your project's models in the console\. 

**To view your models \(console\)**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the left navigation pane, choose **Projects**\. 

1. On the **Projects** page, select the project that contains the models that you want to view\.

1. In the left navigation pane, choose **Models** and then view the model details\.

## Viewing your models \(SDK\)<a name="view-models-sdk"></a>

To get view the versions of a model you use the `ListModels` operation\. To get information about a specific model version, use the `DescribeModel` operation\. The following example lists all the model versions in a project and then displays performance and output configuration information for individual model versions\.

**To view your models \(SDK\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with permissions to access Amazon Lookout for Vision\. For more information, see [Step 3: Set up permissions](su-setup-permissions.md)\. 

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 5: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to list your models and get information about a model\.

------
#### [ CLI ]

   Use the `list-models` command to list the models in a project\.

   Change the following value:
   + `project-name` to the name of the project that contains the model that you want to view\.

   ```
   aws lookoutvision list-models --project-name project name
   ```

   Use the `describe-model` command to get information about a model\. Change the following values:
   + `project-name` to the name of the project that contains the model that you want to view\.
   + `model-version` to the version of the model that you want to describe\.

   ```
   aws lookoutvision describe-model --project-name project name\
       --model-version model version
   ```

------
#### [ Python ]

   In the function `main`, change the following value:
   + `project_name` to the name of the project\. 

   ```
   # Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
   # SPDX-License-Identifier: Apache-2.0
   
   import boto3
   
   from botocore.exceptions import ClientError
   
   def describe_models(project_name):
       """
       Gets information about all models in an Amazon Lookout for Vsion project.
       param: project_name: The name of the project that you want to use.
       """
       try:
   
           client = boto3.client("lookoutvision")
   
           # list models
           response = client.list_models(ProjectName=project_name)
           print("Project: " + project_name)
           for model in response["Models"]:
               print("Model version: " + model["ModelVersion"])
               print("\tARN: " + model["ModelArn"])
               if "Description" in model:
                   print("\tDescription: " + model["Description"])
   
               # Get model description
               model_description = client.describe_model(
                   ProjectName=project_name, ModelVersion=model["ModelVersion"]
               )
               print("\tStatus: " + model_description["ModelDescription"]["Status"])
               print(
                   "\tMessage: " + model_description["ModelDescription"]["StatusMessage"]
               )
               print(
                   "\tCreated: "
                   + str(model_description["ModelDescription"]["CreationTimestamp"])
               )
   
               if model_description["ModelDescription"]["Status"] == "TRAINED":
                   print(
                       "\tTraining duration: "
                       + str(
                           model_description["ModelDescription"]["EvaluationEndTimestamp"]
                           - model_description["ModelDescription"]["CreationTimestamp"]
                       )
                   )
                   print(
                       "\tRecall: "
                       + str(
                           model_description["ModelDescription"]["Performance"]["Recall"]
                       )
                   )
                   print(
                       "\tPrecision: "
                       + str(
                           model_description["ModelDescription"]["Performance"][
                               "Precision"
                           ]
                       )
                   )
                   print(
                       "\tF1: "
                       + str(
                           model_description["ModelDescription"]["Performance"]["F1Score"]
                       )
                   )
                   print(
                       "\tTraining output : s3://"
                       + str(
                           model_description["ModelDescription"]["OutputConfig"][
                               "S3Location"
                           ]["Bucket"]
                       )
                       + "/"
                       + str(
                           model_description["ModelDescription"]["OutputConfig"][
                               "S3Location"
                           ]["Prefix"]
                       )
                   )
   
               print()
   
           print("Done...")
   
       except ClientError as err:
           print("Service error: " + format(err))
           raise
   
   
   def main():
       project_name = "my-project"  # Change to the name of your project.
       describe_models(project_name)
   
   
   if __name__ == "__main__":
       main()
   ```

------