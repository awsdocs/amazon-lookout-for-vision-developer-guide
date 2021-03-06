# Viewing your datasets<a name="view-datasets"></a>

A project can have a single dataset that's used for training and testing your model\. Alternatively, You can have separate training and test datasets\. You can use the console to view your datasets\. You can also use the `DescribeDataset` operation to get information about a dataset \(training or test\)\.

## Viewing the datasets in a project \(console\)<a name="view-datasets-console"></a>

Perform the steps in the following procedure to view your project's datasets in the console\. 

**To view your datasets \(console\)**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the left navigation pane, choose **Projects**\. 

1. On the **Projects** page, select the project that contains the datasets that you want to view\.

1. In the left navigation pane, choose **Dataset** to view the dataset details\. If you have a training and a test dataset, a tab for each dataset is shown\.

## Viewing the datasets in a project \(SDK\)<a name="view-datasets-sdk"></a>

You can get use the `DescribeDataset` operation to get information about the training or test dataset associated with a project\.

**To view your datasets \(SDK\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with permissions to access Amazon Lookout for Vision\. For more information, see [Step 3: Set up permissions](su-setup-permissions.md)\. 

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 5: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to view a dataset\.

------
#### [ CLI ]

   Change the following values:
   + `project-name` to the name of the project that contains the model that you want to view\.
   + `dataset-type` to the type of dataset that you want to view \(`train` or `test`\)\.

   ```
   aws lookoutvision describe-dataset --project-name project name\
     --dataset-type train or test
   ```

------
#### [ Python ]

   In the function `main`, change the following values:
   + `project_name` to the name of the project that contains the model that you want to view\.
   + `dataset_type` to the type of dataset that you want to view \(`train` or `test`\)\.

   ```
   # Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
   # SPDX-License-Identifier: Apache-2.0
   
   import boto3
   
   from botocore.exceptions import ClientError
   
   def describe_dataset(project_name, dataset_type):
       """
       Gets information about an Amazon Lookout for Vision dataset.
       param: project_name: The name of the project that contains the
        dataset that you want to describe.
       param: dataset_type: The type (train or test) of the dataset that you want to describe.
       """
   
       try:
   
           client = boto3.client("lookoutvision")
   
           # Describe a dataset
   
           response = client.describe_dataset(
               ProjectName=project_name, DatasetType=dataset_type
           )
           print("Name: " + response["DatasetDescription"]["ProjectName"])
           print("Type: " + response["DatasetDescription"]["DatasetType"])
           print("Status: " + response["DatasetDescription"]["Status"])
           print("Message: " + response["DatasetDescription"]["StatusMessage"])
           print("Images: " + str(response["DatasetDescription"]["ImageStats"]["Total"]))
           print(
               "Labeled: " + str(response["DatasetDescription"]["ImageStats"]["Labeled"])
           )
           print("Normal: " + str(response["DatasetDescription"]["ImageStats"]["Normal"]))
           print(
               "Anomaly: " + str(response["DatasetDescription"]["ImageStats"]["Anomaly"])
           )
   
           print("Done...")
   
       except ClientError as err:
           print("Service error: " + format(err))
           raise
   
   
   def main():
       project_name = (
           "my-project"  # Change to the project name that contains the dataset.
       )
       dataset_type = "train"  # Change to the dataset type (train or test)
       describe_dataset(project_name, dataset_type)
   
   
   if __name__ == "__main__":
       main()
   ```

------