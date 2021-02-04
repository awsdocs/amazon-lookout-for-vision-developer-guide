--------

Amazon Lookout for Vision is in preview release and is subject to change\.

--------

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

   1. Create or update an IAM user with `AmazonLookoutVisionFullAccess` and permissions\. For more information, see [Step 2: Create an IAM administrator user and group](su-account-user.md)\.

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

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

   Change the following value:
   + `project_name` to the name of the project\. 

   ```
   #Copyright 2021 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   
   import boto3
   import json
   
   def describe_models(project_name):
   
       client=boto3.client('lookoutvision')
   
       try: 
           #list models
           response=client.list_models(ProjectName=project_name)
           print('Project: ' + project_name)
           for model in response['Models']:
               print('Version: ' + model['ModelVersion'])
               print('ARN: ' + model['ModelArn']) 
               if 'Description'  in model:
                   print('Description: ' + model['Description']) 
               print('Status: ' + model['Status']) 
               print('Status Message: ' + model['StatusMessage']) 
   
               # get model description
               model_description=client.describe_model(ProjectName=project_name, ModelVersion=model['ModelVersion'])
               print('Status: ' + model_description['ModelDescription']['Status'])
               print('Message: ' + model_description['ModelDescription']['StatusMessage'])
               
               if 'Performance' in model_description['ModelDescription']:
                   print('Recall: ' + str(model_description['ModelDescription']['Performance']['Recall']))
                   print('Precision: ' + str(model_description['ModelDescription']['Performance']['Precision']))
               
               if 'OutputConfig' in model_description['ModelDescription']:
                   print('Output config: ' + str(model_description['ModelDescription']['OutputConfig']))
               print()
   
           print('Done...')
   
   
       except Exception as e:
           print(e)
       
   def main():
       project_name='my-project'
       describe_models(project_name)
   
   if __name__ == "__main__":
       main()
   ```

------