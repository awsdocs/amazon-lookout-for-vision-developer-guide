# Stopping your Amazon Lookout for Vision model<a name="run-stop-model"></a>

To stop a running model, you call the `StopModel` operation and pass the following:
+ **Project** – The name of the project that contains the model that you want to stop\.
+ **ModelVersion** – The version of the model that you want to stop\.

The Amazon Lookout for Vision console provides example code that you can use to stop a model\. 

**Note**  
You are charged for the amount of the time that your model is running\. 

**Topics**
+ [Stopping your model \(console\)](#stop-model)
+ [Stopping your Amazon Lookout for Vision model \(SDK\)](#stop-model-sdk)

## Stopping your model \(console\)<a name="stop-model"></a>

Perform the steps in the following procedure to stop your model using the console\. 

**To stop your model \(console\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with permissions to access Amazon Lookout for Vision\. For more information, see [Step 3: Set up permissions](su-setup-permissions.md)\. 

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 5: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the left navigation pane, choose **Projects**\.

1. On the **Projects** resources page, choose the project that contains the running model that you want to stop\.

1. In the **Models** section, choose the model that you want to stop\. 

1. On the model's details page, choose **Use model** and then choose **Integrate API to the cloud**\. 

1. Under **AWS CLI commands**, copy the AWS CLI command that calls `stop-model`\.

1. At the command prompt, enter the `stop-model` command that you copied in the previous step\. 

1. At the console, choose **Models** in the left navigation page\.

1. Check the **Status** column for the current status of the model\. The model has stopped when the **Status** column value is **Training complete**\. 

## Stopping your Amazon Lookout for Vision model \(SDK\)<a name="stop-model-sdk"></a>

You stop a model by calling the [StopModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_StopModel) operation\. 

A model might take a while to stop\. To check the current status, use `DescribeModel`\. 

**To stop your model \(SDK\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with permissions to access Amazon Lookout for Vision\. For more information, see [Step 3: Set up permissions](su-setup-permissions.md)\. 

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 5: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to stop a running model\.

------
#### [ CLI ]

   Change the following values: 
   + `project-name` to the name of the project that contains the model that you want to stop\.
   + `model-version` to the version of the model that you want to stop\.

   ```
   aws lookoutvision stop-model --project-name "project name"\
       --model-version model version
   ```

------
#### [ Python ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/python/example_code/lookoutvision/train_host.py)\. 

   ```
       @staticmethod
       def stop_model(lookoutvision_client, project_name, model_version):
           """
           Stops a running Lookout for Vision Model.
   
           :param lookoutvision_client: A Boto3 Lookout for Vision client.
           :param project_name: The name of the project that contains the version of
                                the model that you want to stop hosting.
           :param model_version:  The version of the model that you want to stop hosting.
           """
           try:
               logger.info("Stopping model version %s for %s", model_version, project_name)
               response = lookoutvision_client.stop_model(
                   ProjectName=project_name, ModelVersion=model_version)
               logger.info("Stopping hosting...")
   
               status = response["Status"]
               finished = False
   
               # Wait until stopped or failed.
               while finished is False:
                   model_description = lookoutvision_client.describe_model(
                       ProjectName=project_name, ModelVersion=model_version)
                   status = model_description["ModelDescription"]["Status"]
   
                   if status == "STOPPING_HOSTING":
                       logger.info("Host stopping in progress...")
                       time.sleep(10)
                       continue
   
                   if status == "TRAINED":
                       logger.info("Model is no longer hosted.")
                       finished = True
                       continue
   
                   logger.info("Failed to stop model: %s ", status)
                   finished = True
   
               if status != "TRAINED":
                   logger.error("Error stopping model: %s", status)
                   raise Exception(f"Error stopping model: {status}")
           except ClientError:
               logger.exception("Couldn't stop hosting model.")
               raise
   ```

------
#### [ Java V2 ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/javav2/example_code/lookoutvision/src/main/java/com/example/lookoutvision/StopModel.java)\. 

   ```
   /**
    * Stops the hosting an Amazon Lookout for Vision model. Returns when model has
    * stopped or if hosting fails.
    * 
    * @param lfvClient   An Amazon Lookout for Vision client.
    * @param projectName The name of the project that contains the model that you
    *                    want to stop hosting.
    * @modelVersion The version of the model that you want to stop hosting.
    * @return ModelDescription The description of the model, which includes the
    *         model hosting status.
    */
   
   public static ModelDescription stopModel(LookoutVisionClient lfvClient, String projectName,
                   String modelVersion) throws LookoutVisionException, InterruptedException {
   
           logger.log(Level.INFO, "Stopping Model version {0} for project {1}.",
                           new Object[] { modelVersion, projectName });
   
           StopModelRequest stopModelRequest = StopModelRequest.builder()
                           .projectName(projectName)
                           .modelVersion(modelVersion)
                           .build();
   
           // Stop hosting the model.
   
           lfvClient.stopModel(stopModelRequest);
   
           DescribeModelRequest describeModelRequest = DescribeModelRequest.builder()
                           .projectName(projectName)
                           .modelVersion(modelVersion)
                           .build();
   
           ModelDescription modelDescription = null;
   
           boolean finished = false;
           // Wait until model is stopped or failure occurs.
           do {
   
                   modelDescription = lfvClient.describeModel(describeModelRequest).modelDescription();
   
                   switch (modelDescription.status()) {
   
                           case TRAINED:
                                   logger.log(Level.INFO, "Model version {0} for project {1} has stopped.",
                                                   new Object[] { modelVersion, projectName });
                                   finished = true;
                                   break;
   
                           case STOPPING_HOSTING:
                                   logger.log(Level.INFO, "Model version {0} for project {1} is stopping.",
                                                   new Object[] { modelVersion, projectName });
   
                                   TimeUnit.SECONDS.sleep(60);
   
                                   break;
   
                           default:
                                   logger.log(Level.SEVERE,
                                                   "Unexpected error when stopping model version {0} for project {1}: {2}.",
                                                   new Object[] { projectName, modelVersion,
                                                                   modelDescription.status() });
                                   finished = true;
                                   break;
   
                   }
   
           } while (!finished);
   
           logger.log(Level.INFO, "Finished stopping model version {0} for project {1} status: {2}",
                           new Object[] { modelVersion, projectName, modelDescription.statusMessage() });
   
           return modelDescription;
   
   }
   ```

------