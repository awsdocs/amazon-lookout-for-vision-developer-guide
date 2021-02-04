--------

Amazon Lookout for Vision is in preview release and is subject to change\.

--------

# Deleting a project<a name="delete-project"></a>

You can delete a project from the projects view page in the console or by using the `DeleteProject` operation\. 

The images referenced by a project's datasets aren't deleted\. 

## Deleting a project \(console\)<a name="delete-project-console"></a>

Use the following procedure to delete a project\. If you use the console procedure, associated model versions and datasets are deleted for you\. 

**To delete a project**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the left navigation pane, choose **Projects**\. 

1. On the **Projects** page, select the project that you want to delete\.

1. Choose **Delete** at the top of the page\. 

1. In the **Delete** dialog box, enter **delete** to confirm that you want to delete the project\.

1. If necessary, choose to delete any associated datasets and models\.

1. Choose **Delete project**\. 

## Deleting a project \(SDK\)<a name="delete-project-sdk"></a>

You delete an Amazon Lookout for Vision project by calling [DeleteProject](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DeleteProject) and supplying the name of the project that you want to delete\. 

Before you can delete a project, you must first delete all models in the project\. For more information, see [Deleting a model \(SDK\)](delete-model.md#delete-model-sdk)\. You also have to delete the datasets associated with the model\. For more information, see [Deleting a dataset](delete-dataset.md)\.

The project might take a few moments to delete\. During that time, the status of the project is `DELETING`\. The project is deleted if a subsequent call to `DeleteProject` doesn't include the project that you deleted\.

**To delete a project \(SDK\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with `AmazonLookoutVisionFullAccess` and permissions\. For more information, see [Step 2: Create an IAM administrator user and group](su-account-user.md)\.

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following code to delete a project\. 

------
#### [ AWS CLI ]

   Change the value of `project-name` to the name of the project that you want to delete\.

   ```
   aws lookoutvision delete-project --project-name project_name 
   ```

------
#### [ Python ]

   Change the value of `project_name` to the name of the project you want to delete\.

   ```
   #Copyright 2021 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   
   import boto3
   
   def delete_project(project_name):
   
       client=boto3.client('lookoutvision')
   
       try: 
           #Create a project
           print('Deleting project:' + project_name)
           response=client.delete_project(ProjectName=project_name)
           print('Deleted project ARN: ' + response['ProjectArn'])
           print('Done...')
       
       except Exception as e:
           print(e)
       
   def main():
       project_name='my-project'
   
       delete_project(project_name)
   
   if __name__ == "__main__":
       main()
   ```

------