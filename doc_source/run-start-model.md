# Starting your Amazon Lookout for Vision model<a name="run-start-model"></a>



Before you can use an Amazon Lookout for Vision model to detect anomalies, you must first start the model\. You start a model by calling the [StartModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_StartModel) API and passing the following:
+ **ProjectName** – The name of the project that contains the model that you want to start\.
+ **ModelVersion** – The version of the model that you want to start\.
+ **MinInferenceUnits** – The minimum number of inference units\. For more information, see [Running your trained Amazon Lookout for Vision model](running-model.md)\.

The Amazon Lookout for Vision console provides example code that you can use to start and stop a model\. 

**Note**  
You are charged for the amount of the time that your model is running\. To stop a running model, see [Stopping your Amazon Lookout for Vision model](run-stop-model.md)\. 

**Topics**
+ [Starting your model \(console\)](#start-model-console)
+ [Starting your Amazon Lookout for Vision model \(SDK\)](#start-model-sdk)

## Starting your model \(console\)<a name="start-model-console"></a>

The Amazon Lookout for Vision console provides a AWS CLI command that you can use to start a model\. After the model starts, you can start detecting anomalies in images\. For more information, see [Detecting anomalies in an image](inference-detect-anomalies.md)\.

**To start a model \(console\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with permissions to access Amazon Lookout for Vision\. For more information, see [Step 3: Set up permissions](su-setup-permissions.md)\. 

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 5: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the left navigation pane, choose **Projects**\.

1. On the **Projects** resources page, choose the project that contains the trained model that you want to start\.

1. In the **Models** section, choose the model that you want to start\. 

1. On the model's details page, choose the **Use model** tab\. 

1. Under **Code snippets** choose **AWS CLI commands**\. 

1. Copy the AWS CLI command that calls `start-model`\.

1. At the command prompt, enter the `start-model` command that you copied in the previous step\. 

1. In the console, choose **Models** in the left navigation page\.

1. Check the **Status** column for the current status of the model, When the status is **Hosted**, you can use the model to detect anomalies in images\. For more information, see [Detecting anomalies in an image](inference-detect-anomalies.md)\. 

## Starting your Amazon Lookout for Vision model \(SDK\)<a name="start-model-sdk"></a>

You start a model by calling the [StartModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_StartModel) operation\.

A model might take a while to start\. You can check the current status by calling [DescribeModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DescribeModel)\. For more information, see [Viewing your models](view-models.md)\.

**To start your model \(SDK\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with permissions to access Amazon Lookout for Vision\. For more information, see [Step 3: Set up permissions](su-setup-permissions.md)\. 

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 5: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to start a model\.

------
#### [ CLI ]

   Change the following values:
   + `project-name` to the name of the project that contains the model that you want to start\.
   + `model-version` to the version of the model that you want to start\.
   + `--min-inference-units` to the number of anomaly detection units that you want to use\.

   ```
   aws lookoutvision start-model --project-name "project name"\
     --model-version model version\
     --min-inference-units number of units
   ```

------
#### [ Python ]

   In the function `main`, change the following values:
   + `project` to the name of the project that contains the model that you want to start\.
   + `model_version` to the version of the model that you want to start\. Note that the console prepends the model version with the text `Model`\. You only need to specify the version number\. For example `12`\. 
   + `version_name` to the name of the model that you want to start\.
   + `min_inference_units` to the number of inference units that you want to use\.

   ```
   # Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
   # SPDX-License-Identifier: Apache-2.0
   
   import time
   import boto3
   
   from botocore.exceptions import ClientError
   
   def start_model(project_name, model_version, min_inference_units):
       """
       Starts the hosting of an Amazon Lookout for Vision model.
       param: project_name: The name of the project that contains the version
        of the model that you want to start hosting.
       param: model_version  The version of the model that you want to start hosting.
       param: min_inference_units The number of inference units to use for hosting.
       """
   
       try:
   
           client = boto3.client("lookoutvision")
   
           # Start the model
           print(
               "Starting model version " + model_version + " for project " + project_name
           )
           client.start_model(
               ProjectName=project_name,
               ModelVersion=model_version,
               MinInferenceUnits=min_inference_units,
           )
   
           print("Starting hosting...")
   
           # Wait until either hosted or failed.
           while True:
   
               model_description = client.describe_model(
                   ProjectName=project_name, ModelVersion=model_version
               )
               status = model_description["ModelDescription"]["Status"]
   
               if status == "STARTING_HOSTING":
                   print("Host starting in progress...")
                   time.sleep(10)
                   continue
   
               if status == "HOSTED":
                   print("Model is hosted and ready for use.")
                   break
   
               if status == "HOSTING_FAILED":
                   print("Model hosting failed and the model can't be used.")
                   break
   
               print("Failed. Unexpected state for hosting: " + status)
               break
   
       except ClientError as err:
           print("Service error: " + format(err))
           raise
   
       print("Done...")
   
   
   def main():
       project = "my-project"  # Change to your project name
       model_version = "1"  # Change to the version of your model
       min_inference_units = (
           1  # Change to the number of inference units that you want to use for hosting.
       )
   
       start_model(project, model_version, min_inference_units)
   
   
   if __name__ == "__main__":
       main()
   ```

------

1. If the output of the code is `Model is hosted and ready for use`, you can use the model to detect anomalies in images\. For more information, see [Detecting anomalies in an image](inference-detect-anomalies.md)\.