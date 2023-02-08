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

1. If you haven't already done so, install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following code to delete a project\. 

------
#### [ AWS CLI ]

   Change the value of `project-name` to the name of the project that you want to delete\.

   ```
   aws lookoutvision delete-project --project-name project_name \
     --profile lookoutvision-access
   ```

------
#### [ Python ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/python/example_code/lookoutvision/train_host.py)\. 

   ```
       @staticmethod
       def delete_project(lookoutvision_client, project_name):
           """
           Deletes a Lookout for Vision Model
   
           :param lookoutvision_client: A Boto3 Lookout for Vision client.
           :param project_name: The name of the project that you want to delete.
           """
           try:
               logger.info("Deleting project: %s", project_name)
               response = lookoutvision_client.delete_project(ProjectName=project_name)
               logger.info("Deleted project ARN: %s ", response["ProjectArn"])
           except ClientError as err:
               logger.exception("Couldn't delete project %s.", project_name)
               raise
   ```

------
#### [ Java V2 ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/javav2/example_code/lookoutvision/src/main/java/com/example/lookoutvision/DeleteProject.java)\. 

   ```
   /**
    * Deletes an Amazon Lookout for Vision project.
    * 
    * @param lfvClient   An Amazon Lookout for Vision client.
    * @param projectName The name of the project that you want to create.
    * @return String The ARN of the deleted project.
    */
   public static String deleteProject(LookoutVisionClient lfvClient, String projectName)
                   throws LookoutVisionException {
   
           logger.log(Level.INFO, "Deleting project: {0}", projectName);
   
           DeleteProjectRequest deleteProjectRequest = DeleteProjectRequest.builder()
                           .projectName(projectName)
                           .build();
   
           DeleteProjectResponse response = lfvClient.deleteProject(deleteProjectRequest);
   
           logger.log(Level.INFO, "Deleted project: {0} ARN: {1}",
                           new Object[] { projectName, response.projectArn() });
   
           return response.projectArn();
   
   }
   ```

------