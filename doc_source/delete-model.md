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

1. If you haven't already done so, install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to delete a model\.

------
#### [ CLI ]

   Change the following values:
   + `project-name` to the name of the project that contains the model that you want to delete\.
   + `model-version` to the version of the model that you want to delete\. 

   ```
   aws lookoutvision delete-model --project-name project name\
     --model-version model version \
     --profile lookoutvision-access
   ```

------
#### [ Python ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/python/example_code/lookoutvision/train_host.py)\. 

   ```
       @staticmethod
       def delete_model(lookoutvision_client, project_name, model_version):
           """
           Deletes a Lookout for Vision model. The model must first be stopped and can't
           be in training.
   
           :param lookoutvision_client: A Boto3 Lookout for Vision client.
           :param project_name: The name of the project that contains the desired model.
           :param model_version: The version of the model that you want to delete.
           """
           try:
               logger.info("Deleting model: %s", model_version)
               lookoutvision_client.delete_model(
                   ProjectName=project_name, ModelVersion=model_version)
   
               model_exists = True
               while model_exists:
                   response = lookoutvision_client.list_models(ProjectName=project_name)
   
                   model_exists = False
                   for model in response["Models"]:
                       if model["ModelVersion"] == model_version:
                           model_exists = True
   
                   if model_exists is False:
                       logger.info("Model deleted")
                   else:
                       logger.info("Model is being deleted...")
                       time.sleep(2)
   
               logger.info("Deleted Model: %s", model_version)
           except ClientError:
               logger.exception("Couldn't delete model.")
               raise
   ```

------
#### [ Java V2 ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/javav2/example_code/lookoutvision/src/main/java/com/example/lookoutvision/DeleteModel.java)\. 

   ```
   /**
   * Deletes an Amazon Lookout for Vision model.
   * 
   * @param lfvClient    An Amazon Lookout for Vision client. Returns after the model is deleted.
   * @param projectName  The name of the project that contains the model that you want to delete.
   * @param modelVersion The version of the model that you want to delete.
   * @return void
   */
   public static void deleteModel(LookoutVisionClient lfvClient,
                   String projectName,
                   String modelVersion) throws LookoutVisionException, InterruptedException {
   
           DeleteModelRequest deleteModelRequest = DeleteModelRequest.builder()
                           .projectName(projectName)
                           .modelVersion(modelVersion)
                           .build();
   
           lfvClient.deleteModel(deleteModelRequest);
   
           boolean deleted = false;
   
           do {
   
                   ListModelsRequest listModelsRequest = ListModelsRequest.builder()
                                   .projectName(projectName)
                                   .build();
   
                   // Get a list of models in the supplied project.
                   ListModelsResponse response = lfvClient.listModels(listModelsRequest);
   
                   ModelMetadata modelMetadata = response.models().stream()
                                   .filter(model -> model.modelVersion().equals(modelVersion)).findFirst()
                                   .orElse(null);
   
                   if (modelMetadata == null) {
                           deleted = true;
                           logger.log(Level.INFO, "Deleted: Model version {0} of project {1}.",
                                           new Object[] { modelVersion, projectName });
   
                   } else {
                           logger.log(Level.INFO, "Not yet deleted: Model version {0} of project {1}.",
                                           new Object[] { modelVersion, projectName });
                           TimeUnit.SECONDS.sleep(60);
                   }
   
           } while (!deleted);
   
   }
   ```

------