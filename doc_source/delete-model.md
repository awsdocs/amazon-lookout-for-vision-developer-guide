--------

Amazon Lookout for Vision is in preview release and is subject to change\.

--------

# Deleting a model<a name="delete-model"></a>

You can delete a version of a model by using the console or by using the `DeleteModel` operation\. You can't delete model version that is running or being trained\.

If the model is version running, first use the `StopModel` operation to stop the model version\. For more information, see [Stopping your Amazon Lookout for Vision model](run-stop-model.md)\. If a model is training, wait until it finishes before you delete the model\.

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

   1. Create or update an IAM user with `AmazonLookoutVisionFullAccess` and permissions\. For more information, see [Step 2: Create an IAM administrator user and group](su-account-user.md)\.

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

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

   Change the following values:
   + `project_name` to the name of the project that contains the model that you want to delete\.
   + `model_version` to the version of the model that you want to delete\. 

   ```
   #Copyright 2021 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   
   import boto3
   
   def delete_model(project_name,model_version):
   
       client=boto3.client('lookoutvision')
   
       try: 
           #Delete the model
           print('Deleting model:' + model_version)
           response=client.delete_model(ProjectName=project_name,
             ModelVersion=model_version)
           print('Done...')
       
       except Exception as e:
           print(e)
       
   def main():
       project_name='my-project'
       model_version='model-version'
   
   
       delete_model(project_name, model_version)
   
   if __name__ == "__main__":
       main()
   ```

------