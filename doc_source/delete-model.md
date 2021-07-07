# Deleting a model<a name="delete-model"></a>

You can delete a version of a model by using the console or by using the `DeleteModel` operation\. You can't delete model version that is running or being trained\.

If the model is version running, first use the `StopModel` operation to stop the model version\. For more information, see [Stopping your Amazon Lookout for Vision model](run-stop-model.md)\. If a model is training, wait until it finishes before you delete the model\.

It might take a few seconds to delete a model\. To determine if a model has been deleted, call [ListProjects](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_ListProjects) and check if the version of the model \(`ModelVersion`\) is in the `Models` array\. 

## Deleting a model \(console\)<a name="delete-model-console"></a>

Perform the following steps to delete a model from the console\. 

**To delete a model \(console\)**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the left navigation pane, choose **Projects**\. 

1. On the **Projects** page, select the project that contains the model that you want to delete\.

1. In the left navigation pane, choose **Models**\.

1. On the **models** view, select the radio button for the model that you want to delete\.

1. Choose **Delete** at the top of the page\. 

1. In the **Delete** dialog box, enter **delete** to confirm that you want to delete the model\.

1. Choose **Delete model** to delete the model\.

## Deleting a model \(SDK\)<a name="delete-model-sdk"></a>

Use the following procedure to delete model with the `DeleteModel` operation\. 

**To delete a model \(SDK\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with permissions to access Amazon Lookout for Vision\. For more information, see [Step 3: Set up permissions](su-setup-permissions.md)\. 

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 5: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to delete a model\.

------
#### [ CLI ]

   Change the following values:
   + `project-name` to the name of the project that contains the model that you want to delete\.
   + `model-version` to the version of the model that you want to delete\. 

   ```
   aws lookoutvision delete-model --project-name project name\
     --model-version model version
   ```

------
#### [ Python ]

   In the function `main`, change the following values:
   + `project_name` to the name of the project that contains the model that you want to delete\.
   + `model_version` to the version of the model that you want to delete\. 

   ```
   # Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
   # SPDX-License-Identifier: Apache-2.0
   
   
   import time
   import boto3
   
   from botocore.exceptions import ClientError
   
   
   def delete_model(project_name, model_version):
       """
       Deletes an Amazon Lookout for Vision model. The model must first be
       stopped and can't be in training.
       param: project_name: The name of the project that contains the desired model.
       param: model_version: The version of the model that you want to delete.
       """
   
       try:
           client = boto3.client("lookoutvision")
   
           # Delete the model
           print("Deleting model:" + model_version)
           response = client.delete_model(
               ProjectName=project_name, ModelVersion=model_version
           )
   
           model_exists = True
   
           while model_exists:
               model_exists = False
               response = client.list_models(ProjectName=project_name)
               for model in response["Models"]:
                   if model["ModelVersion"] == model_version:
                       model_exists = True
   
               if model_exists is False:
                   print("Model deleted")
               else:
                   print("Model is being deleted...")
                   time.sleep(2)
   
           print("Deleted Model: " + model_version)
           print("Done...")
   
       except ClientError as err:
           print("Service error: " + format(err))
           raise
   
   
   def main():
       project_name = "my-project"  # The desired project
       model_version = "1"  # The version of the model that you want to delete.
   
       delete_model(project_name, model_version)
   
   
   if __name__ == "__main__":
       main()
   ```

------