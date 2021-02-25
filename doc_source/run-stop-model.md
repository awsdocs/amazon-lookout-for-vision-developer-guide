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

   1. Create or update an IAM user with `AmazonLookoutVisionFullAccess` and permissions\. For more information, see [Step 2: Create an IAM administrator user and group](su-account-user.md)\.

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the left navigation pane, choose **Projects**\.

1. On the **Projects** resources page, choose the project that contains the running model that you want to stop\.

1. In the **Models** section, choose the model that you want to stop\. 

1. On the model's detail page, choose the **Use model** tab\. 

1. Under **Code snippets** choose **AWS CLI commands**\. 

1. Copy the AWS CLI command that calls `stop-model`\.

1. At the command prompt, enter the `stop-model` command that you copied in the previous step\. 

1. At the console, choose **Models** in the left navigation page\.

1. Check the **Status** column for the current status of the model\. The model has stopped when the **Status** column value is **Training complete**\. 

## Stopping your Amazon Lookout for Vision model \(SDK\)<a name="stop-model-sdk"></a>

You stop a model by calling the [StopModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_StopModel) operation\. 

A model might take a while to stop\. To check the current status, use `DescribeModel`\. 

**To stop your model \(SDK\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with `AmazonLookoutVisionFullAccess` and permissions\. For more information, see [Step 2: Create an IAM administrator user and group](su-account-user.md)\.

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

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

   The following example stops a model that is already running\. In the function `main`, change the following values:
   + `project` to the name of the project that contains the model that you want to stop\.
   + `model_version` to the version of the model that you want to stop\.

   ```
   # Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
   # SPDX-License-Identifier: Apache-2.0
   
   import time
   import boto3
   
   from botocore.exceptions import ClientError
   
   def stop_model(project_name, model_version):
       """
       Stops a running Amazon Lookout for Vision Model
       param: project_Name: The name of the project that contains the
            version of the model that you want to stop hosting.
       param: model_version: The version of the nodel that you want to stop hosting.
       """
   
       try:
   
           client = boto3.client("lookoutvision")
   
           # Stop the model
           print(
               "Stopping model version " + model_version + " for project " + project_name
           )
           response = client.stop_model(
               ProjectName=project_name, ModelVersion=model_version
           )
           print("Stopping...")
           status = response["Status"]
   
           # Breaks when hosting has stopped.
           while True:
               model_description = client.describe_model(
                   ProjectName=project_name, ModelVersion=model_version
               )
               status = model_description["ModelDescription"]["Status"]
   
               if status == "STOPPING_HOSTING":
                   print("Host stopping in progress...")
                   time.sleep(10)
                   continue
   
               if status == "TRAINED":
                   print("Model is no longer hosted.")
                   break
   
               print("Failed. Unxexpected state for stopping model: " + status)
               break
       except ClientError as err:
           print("Service error: " + format(err))
           raise
   
       print("Done...")
   
   
   def main():
       project = "my-project"  # Change to your project name.
       model_version = "1"  # Change to the version of your model.
   
       stop_model(project, model_version)
   
   
   if __name__ == "__main__":
       main()
   ```

------