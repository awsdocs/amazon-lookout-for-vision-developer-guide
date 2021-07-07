# Viewing your projects<a name="view-projects"></a>

You can get a list of Amazon Lookout for Vision projects and information about individual projects from the console or by using the AWS SDK\.

## Viewing your projects \(console\)<a name="view-projects-console"></a>

Perform the steps in the following procedure to view your projects in the console\. 

**To view your projects**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the left navigation pane, choose **Projects**\. The projects view is shown\.

1. Choose a project name to see the project's details\.

## Viewing your projects \(SDK\)<a name="view-projects-sdk"></a>

A project manages the datasets and models for a single use case\. For example, detecting anomalies in machine parts\. The following example calls `ListProjects` to get a list of your projects\. The example then calls `DescribeProject` to get the current status of each project\.

**To view your projects \(SDK\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with permissions to access Amazon Lookout for Vision\. For more information, see [Step 3: Set up permissions](su-setup-permissions.md)\. 

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 5: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to view your projects\.

------
#### [ CLI ]

   Use the `list-projects` command to list the projects in your account\.

   ```
   aws lookoutvision list-projects
   ```

   Use the `describe-project` command to get information about a project\.

   Change the value of `project-name` to the name of the project that you want to describe\.

   ```
   aws lookoutvision describe-project --project-name project_name
   ```

------
#### [ Python ]

   ```
   # Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
   # SPDX-License-Identifier: Apache-2.0
   
   
   import boto3
   
   from botocore.exceptions import ClientError
   
   
   def list_projects():
       """
       Lists the projects, models and datasets in your account.
       """
   
       try:
           client = boto3.client("lookoutvision")
   
           response = client.list_projects()
   
           for project in response["Projects"]:
   
               print("Project: " + project["ProjectName"])
               print("\tARN: " + project["ProjectArn"])
               print("\tCreated: " + str(["CreationTimestamp"]))
   
               print("Datasets")
               project_description = client.describe_project(
                   ProjectName=project["ProjectName"]
               )
               if len(project_description["ProjectDescription"]["Datasets"]) == 0:
                   print("\tNo datasets")
               else:
                   for dataset in project_description["ProjectDescription"]["Datasets"]:
                       print("\ttype: " + dataset["DatasetType"])
                       print("\tStatus: " + dataset["StatusMessage"])
   
               print("Models")
               # list models
               response_models = client.list_models(ProjectName=project["ProjectName"])
               if len(response_models["Models"]) == 0:
                   print("\tNo models")
               else:
                   for model in response_models["Models"]:
                       print("\tVersion: " + model["ModelVersion"])
                       print("\tARN: " + model["ModelArn"])
                       if "Description" in model:
                           print("\tDescription: " + model["Description"])
   
                       # Get model description
                       model_description = client.describe_model(
                           ProjectName=project["ProjectName"],
                           ModelVersion=model["ModelVersion"],
                       )
                       print(
                           "\tStatus: " + model_description["ModelDescription"]["Status"]
                       )
                       print(
                           "\tMessage: "
                           + model_description["ModelDescription"]["StatusMessage"]
                       )
                       print(
                           "\tCreated: "
                           + str(
                               model_description["ModelDescription"]["CreationTimestamp"]
                           )
                       )
   
                       if model_description["ModelDescription"]["Status"] == "TRAINED":
                           print(
                               "\tTraining duration: "
                               + str(
                                   model_description["ModelDescription"][
                                       "EvaluationEndTimestamp"
                                   ]
                                   - model_description["ModelDescription"][
                                       "CreationTimestamp"
                                   ]
                               )
                           )
                           print(
                               "\tRecall: "
                               + str(
                                   model_description["ModelDescription"]["Performance"][
                                       "Recall"
                                   ]
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
                                   model_description["ModelDescription"]["Performance"][
                                       "F1Score"
                                   ]
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
   
               print()
   
       except ClientError as err:
           print("Service error: " + format(err))
           raise
   
       print("Done...")
   
   
   def main():
       list_projects()
   
   
   if __name__ == "__main__":
       main()
   ```

------