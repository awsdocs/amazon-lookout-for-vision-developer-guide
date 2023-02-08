# Starting your Amazon Lookout for Vision model<a name="run-start-model"></a>



Before you can use an Amazon Lookout for Vision model to detect anomalies, you must first start the model\. You start a model by calling the [StartModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_StartModel) API and passing the following:
+ **ProjectName** – The name of the project that contains the model that you want to start\.
+ **ModelVersion** – The version of the model that you want to start\.
+ **MinInferenceUnits** – The minimum number of inference units\. For more information, see [Inference units](running-model.md#running-model-inference-units)\.
+ \(Optional\) **MaxInferenceUnits** – The maximum number of inference units that Amazon Lookout for Vision can use to automatically scale the model\. For more information, see [Auto\-scale inference units](running-model.md#running-model-auto-scale-inference-units)\.

The Amazon Lookout for Vision console provides example code that you can use to start and stop a model\. 

**Note**  
You are charged for the amount of the time that your model is running\. To stop a running model, see [Stopping your Amazon Lookout for Vision model](run-stop-model.md)\.   
You can use the AWS SDK to view running models across all AWS Regions in which Lookout for Vision is available\. For example code, see [find\_running\_models\.py](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/python/example_code/lookoutvision/find_running_models.py)\.

**Topics**
+ [Starting your model \(console\)](#start-model-console)
+ [Starting your Amazon Lookout for Vision model \(SDK\)](#start-model-sdk)

## Starting your model \(console\)<a name="start-model-console"></a>

The Amazon Lookout for Vision console provides a AWS CLI command that you can use to start a model\. After the model starts, you can start detecting anomalies in images\. For more information, see [Detecting anomalies in an image](inference-detect-anomalies.md)\.

**To start a model \(console\)**

1. If you haven't already done so, install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the left navigation pane, choose **Projects**\.

1. On the **Projects** resources page, choose the project that contains the trained model that you want to start\.

1. In the **Models** section, choose the model that you want to start\. 

1. On the model's details page, choose **Use model** and then choose **Integrate API to the cloud**\. 
**Tip**  
If you want to deploy your model to an edge device, choose **Create model packaging job**\. For more information, see [Packaging your Amazon Lookout for Vision model](package-job.md)\.

1. Under **AWS CLI commands**, copy the AWS CLI command that calls `start-model`\.

1. At the command prompt, enter the `start-model` command that you copied in the previous step\. If you are using the `lookoutvision` profile to get credentials, add the `--profile lookoutvision-access` parameter\. 

1. In the console, choose **Models** in the left navigation page\.

1. Check the **Status** column for the current status of the model, When the status is **Hosted**, you can use the model to detect anomalies in images\. For more information, see [Detecting anomalies in an image](inference-detect-anomalies.md)\. 

## Starting your Amazon Lookout for Vision model \(SDK\)<a name="start-model-sdk"></a>

You start a model by calling the [StartModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_StartModel) operation\.

A model might take a while to start\. You can check the current status by calling [DescribeModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DescribeModel)\. For more information, see [Viewing your models](view-models.md)\.

**To start your model \(SDK\)**

1. If you haven't already done so, install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to start a model\.

------
#### [ CLI ]

   Change the following values:
   + `project-name` to the name of the project that contains the model that you want to start\.
   + `model-version` to the version of the model that you want to start\.
   + `--min-inference-units` to the number of inference units that you want to use\.
   + \(Optional\) `--max-inference-units` to the maximum number of inference units that Amazon Lookout for Vision can use to automatically scale the model\. 

   ```
   aws lookoutvision start-model --project-name "project name"\
     --model-version model version\
     --min-inference-units minimum number of units\
     --max-inference-units max number of units \
     --profile lookoutvision-access
   ```

------
#### [ Python ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/python/example_code/lookoutvision/train_host.py)\. 

   ```
       @staticmethod
       def start_model(
               lookoutvision_client, project_name, model_version, min_inference_units, max_inference_units = None):
           """
           Starts the hosting of a Lookout for Vision model.
   
           :param lookoutvision_client: A Boto3 Lookout for Vision client.
           :param project_name:  The name of the project that contains the version of the
                                 model that you want to start hosting.
           :param model_version: The version of the model that you want to start hosting.
           :param min_inference_units: The number of inference units to use for hosting.
           :param max_inference_units: (Optional) The maximum number of inference units that
           Lookout for Vision can use to automatically scale the model.
           """
           try:
               logger.info(
                   "Starting model version %s for project %s", model_version, project_name)
               
               if max_inference_units is None:
                   lookoutvision_client.start_model(
                       ProjectName = project_name,
                       ModelVersion = model_version,
                       MinInferenceUnits = min_inference_units)
   
               else:
                   lookoutvision_client.start_model(
                       ProjectName = project_name,
                       ModelVersion = model_version,
                       MinInferenceUnits = min_inference_units,
                       MaxInferenceUnits = max_inference_units)
   
               print("Starting hosting...")
   
               status = ""
               finished = False
   
               # Wait until hosted or failed.
               while finished is False:
                   model_description = lookoutvision_client.describe_model(
                       ProjectName=project_name, ModelVersion=model_version)
                   status = model_description["ModelDescription"]["Status"]
   
                   if status == "STARTING_HOSTING":
                       logger.info("Host starting in progress...")
                       time.sleep(10)
                       continue
   
                   if status == "HOSTED":
                       logger.info("Model is hosted and ready for use.")
                       finished = True
                       continue
   
                   logger.info("Model hosting failed and the model can't be used.")
                   finished = True
   
               if status != "HOSTED":
                   logger.error("Error hosting model: %s", status)
                   raise Exception(f"Error hosting model: {status}")
           except ClientError:
               logger.exception("Couldn't host model.")
               raise
   ```

------
#### [ Java V2 ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/javav2/example_code/lookoutvision/src/main/java/com/example/lookoutvision/StartModel.java)\. 

   ```
   /**
    * Starts hosting an Amazon Lookout for Vision model. Returns when the model has
    * started or if hosting fails. You are charged for the amount of time that a
    * model is hosted. To stop hosting a model, use the StopModel operation.
    * 
    * @param lfvClient   An Amazon Lookout for Vision client.
    * @param projectName The name of the project that contains the model that you
    *                    want to host.
    * @modelVersion The version of the model that you want to host.
    * @minInferenceUnits The number of inference units to use for hosting.
    * @maxInferenceUnits The maximum number of inference units that Lookout for
    *                    Vision can use for automatically scaling the model. If the
    *                    value is null, automatic scaling doesn't happen.
    * @return ModelDescription The description of the model, which includes the
    *         model hosting status.
    */
   public static ModelDescription startModel(LookoutVisionClient lfvClient, String projectName, String modelVersion,
           Integer minInferenceUnits, Integer maxInferenceUnits) throws LookoutVisionException, InterruptedException {
   
       logger.log(Level.INFO, "Starting Model version {0} for project {1}.",
               new Object[] { modelVersion, projectName });
   
       StartModelRequest startModelRequest = null;
   
       if (maxInferenceUnits == null) {
   
           startModelRequest = StartModelRequest.builder().projectName(projectName).modelVersion(modelVersion)
                   .minInferenceUnits(minInferenceUnits).build();
       } else {
           startModelRequest = StartModelRequest.builder().projectName(projectName).modelVersion(modelVersion)
                   .minInferenceUnits(minInferenceUnits).maxInferenceUnits(maxInferenceUnits).build();
       }
   
       // Start hosting the model.
       lfvClient.startModel(startModelRequest);
   
       DescribeModelRequest describeModelRequest = DescribeModelRequest.builder().projectName(projectName)
               .modelVersion(modelVersion).build();
   
       ModelDescription modelDescription = null;
   
       boolean finished = false;
       // Wait until model is hosted or failure occurs.
       do {
   
           modelDescription = lfvClient.describeModel(describeModelRequest).modelDescription();
   
           switch (modelDescription.status()) {
   
           case HOSTED:
               logger.log(Level.INFO, "Model version {0} for project {1} is running.",
                       new Object[] { modelVersion, projectName });
               finished = true;
               break;
   
           case STARTING_HOSTING:
               logger.log(Level.INFO, "Model version {0} for project {1} is starting.",
                       new Object[] { modelVersion, projectName });
   
               TimeUnit.SECONDS.sleep(60);
   
               break;
           case HOSTING_FAILED:
               logger.log(Level.SEVERE, "Hosting failed for model version {0} for project {1}.",
                       new Object[] { modelVersion, projectName });
               finished = true;
               break;
   
           default:
               logger.log(Level.SEVERE, "Unexpected error when hosting model version {0} for project {1}: {2}.",
                       new Object[] { projectName, modelVersion, modelDescription.status() });
               finished = true;
               break;
   
           }
   
       } while (!finished);
   
       logger.log(Level.INFO, "Finished starting model version {0} for project {1} status: {2}",
               new Object[] { modelVersion, projectName, modelDescription.statusMessage() });
   
       return modelDescription;
   
   }
   ```

------

1. If the output of the code is `Model is hosted and ready for use`, you can use the model to detect anomalies in images\. For more information, see [Detecting anomalies in an image](inference-detect-anomalies.md)\.